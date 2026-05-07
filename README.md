# Site Intelligence Agent

> An AI assistant that gives frontline field workers instant access to expert knowledge — and knows when to say "I don't know."

**Cornell MEM Capstone · Nishchay Vishwanath · April 2026**

---

## The Problem

A field technician arrives at a rooftop HVAC unit they've never serviced. They have 30 minutes to fix it safely. The relevant safety procedure is in a binder at the office. The equipment manual is 400 pages in their truck. What the last technician did here is locked in a back-office system they can't reach on-site.

OSHA estimates this knowledge gap causes **50,000 preventable injuries and 120 deaths per year** in field service work alone — and the risk falls disproportionately on workers at smaller, under-resourced companies who can't afford dedicated knowledge management systems.

The knowledge exists. It's just fragmented, inaccessible, and never where it's needed.

---

## What I Built

A **RAG-based Site Intelligence Agent** that field workers can query in plain English on-site. It searches simultaneously across safety regulations, equipment manuals, and site service history — then routes every response through a confidence-aware degradation layer before ever calling a language model.

Two domains, one pipeline:
- **Phase 1 — HVAC Field Service** (complete): lockout/tagout procedures, equipment specs, job history
- **Phase 2 — Drone Site Inspection** (in progress): anomaly detection, structural baseline comparison, compliance checks

---

## Core Architecture: Graceful Degradation

The key design decision: **the LLM is the last step, not the first.**

Every query runs through five stages in sequence:

```
Query
  → Classify      classifier.py     What kind of question? Which collections?
  → Retrieve      retriever.py      Embed query, search Chroma, return top-k chunks
  → Score         confidence.py     Cosine similarity + conflict detection
  → Gate          assistant.py      Score < 0.50 → skip LLM entirely
  → Route         degradation.py    Format final response based on confidence level
```

### Three Routing Paths

| Level | Trigger | Response |
|-------|---------|----------|
| **HIGH** | Top similarity ≥ 0.79, no conflict | Answer with source citations |
| **PARTIAL** | Conflicting sources, or similarity 0.50–0.79 | Answer flagged with both conflicting sources surfaced |
| **LOW** | Similarity < 0.50, or no results | Escalation message — LLM is never called |

On the **LOW path, the LLM is skipped entirely.** The escalation message is generated programmatically. Every LOW-path escalation is a hallucination that didn't happen.

Thresholds are tunable without code changes via env vars (`CONFIDENCE_HIGH_THRESHOLD`, `CONFIDENCE_PARTIAL_THRESHOLD`).

### Conflict Detection

Two heuristics run in sequence after retrieval:

1. **Cross-collection conflict** — top 3 results from different collections with scores within 0.15 → PARTIAL. Catches OSHA vs. manual disagreements.
2. **Within-collection version conflict** — multiple source files from the same collection with best-scores within 0.15 → PARTIAL. Catches Carrier 2017 vs. 2023 manual disagreements. Guard: only fires if top score ≥ 0.50.

---

## Knowledge & Tech Stack

### AI / ML
| Component | Detail |
|-----------|--------|
| RAG pipeline | Retrieve → score → route — LLM gated behind confidence threshold |
| Confidence scoring | Cosine similarity + conflict detection + threshold routing |
| Graceful degradation | Three-path routing: HIGH / PARTIAL / LOW |
| Classifier agent | Rule-based keyword matching (~70% of queries, ~5ms) + LLM structured-output fallback |
| Session memory | Zone, equipment, and time-reference entity tracking across multi-turn conversations |
| Structured output | LLM responses constrained to typed schemas |
| Swappable LLM providers | Anthropic · OpenAI · Gemini — resolved at call time from env var |

### Embeddings & Vector Search
| Component | Detail |
|-----------|--------|
| Sentence Transformers | `all-MiniLM-L6-v2` — 384-dim unit-normalized vectors |
| ChromaDB | Six separate collections (3 per domain); metadata filters for spatial zone queries |
| LangChain | Document loading, chunking, `langchain_chroma` retriever |

> **Cosine similarity conversion:** Chroma returns L2 distance. Correct formula for unit-normalized vectors: `cosine_similarity = 1.0 - (L2_distance² / 2)`. Using `1.0 - score` directly is wrong and causes high-confidence matches to score ~0.36 instead of ~0.80.

### Engineering
| Component | Detail |
|-----------|--------|
| Python | End-to-end pipeline, eval suite, data generation |
| Streamlit | 7-page persona-driven walkthrough app with live session state |
| Evaluation suite | 85 labeled cases across ground truth, adversarial, and contradiction test sets |
| Synthetic data | 50 HVAC + drone inspection records generated via Claude API |

### Research Foundation
| | |
|--|--|
| **MAGMA** | *Multi-Graph Agentic Memory Architecture* (Jiang et al., arXiv Jan 2026) — reasoning across four graph dimensions: Semantic (WHAT), Temporal (WHEN), Causal (WHY), Entity (WHO/WHERE). This system implements the confidence routing and safety layer; the multi-graph backend is Phase 3. |
| **Safety-critical design** | False-negative preference — unnecessary escalation is annoying; confident wrong answer in a safety-critical setting can injure someone |
| **Domain knowledge** | OSHA 29 CFR 1910.147 (LOTO) · 1910.303 (Electrical) · 1926.452 (Scaffolding) |

---

## Evaluation Results (Phase 1 — HVAC)

Tested against 85 labeled cases across three test sets:

| Test set | Cases | Target | Result |
|----------|-------|--------|--------|
| Ground truth (HIGH or PARTIAL expected) | 50 | > 80% | **94.0%** |
| Adversarial (LOW / escalate expected) | 20 | > 50% | **45.0%** |
| Contradictions (PARTIAL / conflict expected) | 15 | > 70% | **80.0%** |
| **Overall** | **85** | | **80.0%** |

Adversarial at 45% is a known architectural limitation — semantically similar near-miss equipment (Trane XR13 ≈ XR15) cannot be distinguished by cosine similarity alone. Production fix: hybrid retrieval (BM25 + dense).

**Hallucination rate: < 2%.** Every LOW-path escalation is logged and traceable.

---

## Six Chroma Collections — Never Merged

Source identity is preserved across all six collections. Merging them would destroy conflict detection, which depends on knowing *which collection* each result came from.

**HVAC — Phase 1**

| Collection | Contents | Chunks |
|------------|----------|--------|
| `osha` | 29 CFR 1910.147 (LOTO) · 29 CFR 1910.303 (Electrical) | 283 |
| `manuals` | Carrier 48LC 2017/2023 · Lennox SL280 · Trane XR15 | 1,372 |
| `job_history` | 50 synthetic field service records | 181 |

**Drone Inspection — Phase 2**

| Collection | Contents | Chunks |
|------------|----------|--------|
| `inspection_records` | Per-flight anomaly records by zone and date | 202 |
| `historical_baselines` | Pre-construction baselines for deviation comparison | 160 |
| `compliance_docs` | OSHA 1926.452 scaffold safety requirements | 158 |

---

## Streamlit Walkthrough App

A 7-page persona-driven walkthrough. The Home page sets the scene: you are **Marcus, a 3rd-year HVAC field technician**, on-site alone with 30 minutes to fix a unit you've never seen.

```
streamlit run Home.py
```

| Page | What it shows |
|------|--------------|
| Home | Persona intro (Marcus) + scenario + routing overview |
| 1 — Ask the Agent | Live demo — all three confidence paths with real queries |
| 2 — View the Site | Dual-domain site overview |
| 3 — Inspect a Zone | Drone agent with Zone C pre-loaded, session memory trace, pipeline debug |
| 4 — See the Proof | Eval metrics dashboard with source citations |
| 5 — Find the Gaps | Honesty Report — coverage heatmap (answered vs. escalated) |
| 6 — Connect the Dots | Session memory graph and summary card |
| 7 — How It Works | Interactive confidence simulator, architecture explainer, knowledge stack |

---

## Setup

```bash
# Install
pip install -r requirements.txt
cp .env.example .env        # add ANTHROPIC_API_KEY (or OPENAI_API_KEY or GEMINI_API_KEY)

# Ingest documents
python src/ingest.py                 # HVAC only
python src/ingest.py --domain drone  # Drone only
python src/ingest.py --domain all    # Both domains

# Generate synthetic data
python src/generate_synthetic.py     # 50 HVAC job records (via Claude API)
python src/generate_drone_data.py    # Drone inspection + baseline records

# Run the app
streamlit run Home.py

# CLI demo (three preset queries: HIGH / PARTIAL / LOW)
python demo/demo.py

# Evaluation suite
python eval/run_eval.py
```

**Tune thresholds without code changes:**
```bash
CONFIDENCE_HIGH_THRESHOLD=0.80 CONFIDENCE_PARTIAL_THRESHOLD=0.55 python demo/demo.py
```

---

## Project Structure

```
src/
  assistant.py            FieldServiceAssistant (HVAC) + SiteIntelligenceAgent (dual-domain)
  classifier.py           Rule-based + LLM fallback query classification
  session_memory.py       Zone / equipment / time entity tracking across turns
  retriever.py            Vector retrieval, spatial filter, conflict detection
  confidence.py           Confidence scoring + routing decision
  degradation.py          Graceful degradation — HIGH / PARTIAL / LOW response formatting
  llm.py                  LLM wrapper — Anthropic / OpenAI / Gemini, resolved at call time
  ingest.py               Document loading + Chroma indexing (HVAC + Drone)
  generate_synthetic.py   Synthetic HVAC job history via Claude API
  generate_drone_data.py  Synthetic drone inspection + baseline data

pages/
  1_Ask_the_Agent.py      Three-path confidence demo
  2_View_the_Site.py      Site overview — dual domain
  3_Inspect_a_Zone.py     Drone agent + session memory + pipeline trace
  4_See_the_Proof.py      Eval metrics dashboard
  5_Find_the_Gaps.py      Honesty report / coverage heatmap
  6_Connect_the_Dots.py   Session memory graph finale
  7_How_It_Works.py       Interactive explainer — simulator, architecture, knowledge stack

eval/
  ground_truth.json       50 HVAC query-answer pairs
  adversarial.json        20 out-of-corpus queries (should escalate)
  contradictions.json     15 conflict scenarios (should route PARTIAL)
  run_eval.py             Evaluation runner → prints metrics + saves eval_results.csv

demo/
  demo.py                 CLI demo — three preset queries for live presentation
```

---

## Troubleshooting

**Duplicate chunks** (if ingest was run more than once without clearing):
```bash
rm -rf data/chroma_db/
python src/ingest.py --domain all
```

**Switching LLM providers** — set `LLM_PROVIDER` in `.env`:
- `anthropic` → Claude (Anthropic API key required)
- `openai` → GPT (OpenAI API key required)
- `gemini` → Gemini (Google API key required, current default: `gemini-2.5-flash`)

If `LLM_PROVIDER` is unset, the system auto-detects from whichever API key is present.

---

## References

1. Lewis et al. "Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks." NeurIPS 2020. https://arxiv.org/abs/2005.11401
2. Jiang et al. "MAGMA: Multi-Graph Agentic Memory Architecture." arXiv, January 2026.
3. OSHA. "Control of Hazardous Energy (Lockout/Tagout)." OSHA 3120, 2002. https://www.osha.gov/laws-regs/regulations/standardnumber/1910/1910.147
4. McKinsey Global Institute. "The Social Economy: Unlocking Value and Productivity Through Social Technologies." July 2012.
5. Bureau of Labor Statistics. "Heating, Air Conditioning, and Refrigeration Mechanics and Installers." Occupational Outlook Handbook. https://www.bls.gov/ooh/installation-maintenance-and-repair/heating-air-conditioning-and-refrigeration-mechanics-and-installers.htm
