# Product Discovery Document
**Site Intelligence Agent — Social Impact Edition**
Nishchay Vishwanath · Cornell 2026
Hackathon Theme: Economic Empowerment & Education

---

## Product Position

**Site Intelligence** is the layer between raw site data and trusted decisions.

Every physical site — a rooftop HVAC unit, a bridge zone, a construction inspection area — generates fragmented data: manufacturer manuals, OSHA standards, inspection records, service histories. That data exists. The problem is that no one can synthesize it at the point of need, in the time available, for the person who needs it most.

This system does.

> **"Site Intelligence — AI that shows you the site, surfaces the risk, and tells you when not to trust its own answer."**

Everything in this document supports that sentence. HVAC and drone inspection are both *sites* — two examples of the same problem. MAGMA-inspired multi-graph memory is *how* the system reasons. Social impact is *why* it matters. Graceful degradation is the *trust layer*. None of these compete. They each have a role.

---

## The Three Visible Experiences

The demo, the UX, and the product story all organize around three moments a user has with the system:

### 1. See the Site
You don't open a chat interface. You open a site map. Zones are color-coded by anomaly severity and inspection confidence. Zone C is flagged. Zone X has no data. Click a zone — the agent already knows about it. The entry point is spatial, not conversational. This separates the product from every "chatbot with a search bar" demo.

*Design principle: The site comes first. The chat is secondary.*

### 2. Understand the Risk
Risk is two things here, and the product shows both. First, physical risk: what anomalies exist, what the inspection record says, what the baseline comparison shows. Second, epistemic risk: does the system know enough to act? The Knowledge Gap Map makes this visible — zones in red are where the AI doesn't have sufficient data to answer with confidence. The Pipeline Trace shows what was searched, what was found, and whether sources conflict. A PARTIAL response surfaces the conflicting sources side-by-side. The user never has to guess whether the system found good information.

*Design principle: Risk is both physical (the anomaly) and epistemic (does the AI know enough?). Show both.*

### 3. Know Whether to Trust the Answer
The HIGH / PARTIAL / LOW confidence badge is always visible. But trust goes deeper: "Why does the AI know this?" The MAGMA Memory Graph shows the causal chain that produced the answer — which inspection events were traversed, which sources were cited, what the temporal sequence of anomalies looks like. When the answer is LOW confidence, the system doesn't disappear into a hallucination. It shows what was searched, what wasn't found, and what the recommended next action is. The reasoning is always inspectable.

*Design principle: Trust is earned by showing the reasoning, not just the answer.*

---

## The Problem

### Hook
A 22-year-old HVAC technician is dispatched to their fourth job of the day — a rooftop unit they've never serviced, at a building they've never visited. The equipment manual is 400 pages in the truck. The OSHA standard for the lockout/tagout procedure is in a binder at the office. The last technician's service notes are in a system no one can access from the field.

They have 30 minutes to diagnose, confirm safety, and fix it correctly.

This is not a technology problem. It is a synthesis problem. The data exists. It is fragmented across too many sources to be useful at the point of need — and that gap falls hardest on the people who can afford it least.

### Scale

- **425,200** HVAC mechanics and installers employed in the US in 2024, with employment projected to grow 8% through 2034 [1]
- **20 million** field service technicians operating globally across electrical, plumbing, telecom, infrastructure, and facilities management [2]
- OSHA estimates proper lockout/tagout compliance alone prevents **120 deaths and 50,000 injuries per year** — and approximately **3 million U.S. workers** regularly service equipment that exposes them to hazardous energy [3]
- Industry-wide first-time fix rate averages **77–80%**; organizations below 70% face documented adverse effects on customer retention, SLA compliance, and asset uptime — meaning 1 in 5 service calls requires at least one return trip [4]
- Knowledge workers spend an average of **1.8 hours per day** — nearly a quarter of the workweek — searching and gathering information; for field technicians without mobile-accessible knowledge systems, the impact is compounded by connectivity constraints and time pressure [5]

### Why Existing Tools Fail

The problem is not access to data. The problem is synthesis. Answering "how do I service THIS Carrier RTU at THIS building safely today?" requires pulling from the manufacturer manual, the site's maintenance history, the relevant OSHA standard, and the site's prior service records — simultaneously, in seconds, on a rooftop.

| Workaround | Why It Fails |
|---|---|
| Google | Multi-source synthesis cannot be Googled |
| Call the office | The dispatcher doesn't have the technical depth; engineers aren't available for every call |
| Search the manual | 400+ pages, not indexed, often the wrong model |
| Ask a colleague | Knowledge is individual; the retiring workforce takes it with them |
| Enterprise FSM software | Custom enterprise pricing — small shops lack the budget and infrastructure required [6] |

---

## Users

### Tier 1 — The Junior Field Technician
**The Social Impact Hero / Primary End User**

Vocational-trained, working-class background, 1–5 years of experience. Dispatched to 3–6 sites per day — different equipment, manufacturers, building codes, service histories. Works on rooftops, in basements, mechanical rooms: limited connectivity, time pressure, often alone.

**Jobs to be Done:**
- "Tell me the lockout/tagout procedure for this specific unit, right now"
- "What did the last technician do here and why?"
- "Is this reading within spec for this model?"
- "This anomaly looks wrong — is it normal for this equipment?"

**Pain Points:**
- OSHA estimates that failures to control hazardous energy cause 120 preventable deaths and 50,000 preventable injuries annually; workers injured in these incidents lose an average of **24 workdays** per incident [3]
- Knowledge workers spend 1.8 hours/day searching for information [5]; field technicians face the same problem with no desk, no reliable internet, and a 30-minute job clock
- Mistakes caused by missing information are attributed to technician error, not system failure — wage and career consequences fall on the worker

### Tier 2 — The Small Shop Owner / Operations Manager
**The Economic Buyer**

50–200 technicians, dozens of active service contracts. Competing against larger companies with better-resourced knowledge infrastructure. Cannot absorb enterprise FSM pricing.

**Jobs to be Done:**
- "Reduce first-call resolution failures without hiring a full-time training coordinator"
- "Give junior techs the confidence to handle unfamiliar equipment independently"
- "Know which knowledge gaps are causing the most missed diagnoses"
- "Reduce liability exposure from compliance mistakes in the field"

**Pain Points:**
- First-time fix rates and safety incidents directly affect contract renewal and margins
- HVAC certificate programs cost $1,200–$15,000; trade school programs average $15,000–$20,000 per technician [7] — and experienced mentors are retiring
- Each unnecessary return visit costs $150–$500 in direct costs; fully-loaded cost reaches $800–$1,000 per incident [8]

**Value Proposition (Quantified):**
If the tool saves each technician 30 minutes per day — consistent with research showing knowledge access tools cut search time by up to 35% [5] — and improves first-time fix rates by 5 percentage points, a 200-technician organization avoids an estimated **$1.5M annually** in combined labor waste and return-visit costs. *(Derived estimate based on median HVAC technician wage of $59,810/year [1] and truck roll cost benchmarks [8].)*

### Tier 3 — The Municipality / Infrastructure Manager
**The Governance & Public Safety User**

City or county engineer responsible for public infrastructure. Receives drone inspection data but lacks the expert staff to convert it into actionable maintenance decisions.

**Jobs to be Done:**
- "Know which zones need attention before something fails publicly"
- "Justify infrastructure budget decisions to elected officials with data, not intuition"
- "Make drone inspection data actionable without hiring expensive analysts"

**Pain Points:**
- Post-flight drone data requires expert manual review to be actionable; many municipalities treat drone funding as discretionary rather than operational [9]
- The global drone inspection and monitoring market is growing at 15–19% CAGR [9] but the intelligence layer that converts raw flight data into decisions remains expensive and expert-dependent
- Decisions about which infrastructure gets repaired are driven by budget, not safety data, because safety data is inaccessible

---

## Pain Points Summary

| Pain | Evidence | Citation | Who | Severity |
|------|----------|----------|-----|----------|
| Information fragmentation at point of need | Multi-source synthesis cannot be Googled | — | Tier 1 | Critical |
| Time lost searching for information | 1.8 hrs/day; tools cut this 35% | [5] | Tier 1 | High |
| Safety incidents from hazardous energy failures | 120 deaths + 50,000 injuries/yr preventable | [3] | Tier 1 | Critical |
| Sub-optimal first-time fix rate | Industry avg 77–80%; below 70% signals systemic issues | [4] | Tier 1, 2 | High |
| Cost of return visits | $150–$500 direct; $800–$1,000 fully loaded | [8] | Tier 2 | High |
| Training cost per technician | Certificate: $1,200–$15,000; trade school: $15k–$20k | [7] | Tier 2 | High |
| Retiring workforce knowledge loss | Unstructured; leaves with the person | — | Tier 1, 2 | Medium |
| Inspection data unusable post-flight | Requires expert manual review | [9] | Tier 3 | High |
| Infrastructure monitoring access gap | Cities can't fund in-house drone intelligence | [9] | Public | Critical |

---

## How It Works

### The Memory Layer: MAGMA-Inspired Multi-Graph Architecture

Standard RAG returns documents that *look like* the query. That's semantic similarity — useful, but insufficient for site intelligence. "Why has Zone C kept failing?" is a causal question, not a semantic one. "When was Zone B last above baseline?" is a temporal question. "Which zones has Inspector Patel worked on?" is an entity question.

Inspired by *MAGMA: A Multi-Graph based Agentic Memory Architecture for AI Agents* (Jiang, Li, Li, Li; arXiv:2601.03236v1, January 2026) [10], the system maintains four orthogonal relation graphs over the site's history:

| Graph | What it encodes | Query type |
|---|---|---|
| **Temporal** | Strict chronological ordering of site events | WHEN |
| **Causal** | Directed anomaly → maintenance → resolution chains | WHY |
| **Entity** | Zones, equipment, inspectors tracked across time | WHO / WHERE |
| **Semantic** | Conceptually similar events across zones | WHAT |

An intent-aware classifier routes each query to the appropriate graph. A Heuristic Beam Search traversal policy expands from anchor nodes using edge-type weights calibrated to the detected intent. This achieves **0.700 overall accuracy** on the LoCo-Mo long-horizon reasoning benchmark — vs. 0.481 for full-context baseline and 0.590 for the next best system — while using 95% fewer tokens per query [10].

### The Trust Layer: Graceful Degradation

After retrieval and graph traversal, every response is confidence-scored and routed to one of three explicit paths:

- **HIGH:** Answer with citation — the information is clearly present and uncontested
- **PARTIAL:** Surface the conflict — two sources disagree; human judgment required
- **LOW:** Escalate without answering — the information is not in the corpus

**The LOW path never calls the LLM.** The escalation message is programmatic. This is the safety feature:

> "An AI that confidently answers a technician about to service high-voltage equipment, when it doesn't actually know — that's not a useful tool. It's a liability. The system escalates before it guesses."

MAGMA's adversarial performance validates this design: structured graph traversal scores **0.742** on adversarial queries vs. **0.205** for full-context baseline — because causal and entity-consistent paths avoid the semantically similar but structurally irrelevant results that cause hallucinations [10].

---

## Social Impact

Site Intelligence is positioned under the **Economic Empowerment & Education** theme because the knowledge gap it closes is structural, not individual.

The knowledge gap between a 22-year-old junior tech and a 20-year veteran is not a matter of ability. The veteran knows where to look, who to call, and what the site history means. The junior tech doesn't — because that knowledge was never made accessible. Enterprise companies invest in training coordinators, knowledge management systems, and experienced mentors. Small shops don't. The outcomes diverge accordingly: lower first-time fix rates, higher incident exposure, slower career progression.

This system democratizes the intelligence layer. The same answer that a veteran tech would derive in 5 minutes from memory and experience is available to a junior tech in 10 seconds, with citations.

**Safety Equity:** OSHA estimates 120 preventable deaths and 50,000 preventable injuries annually from hazardous energy control failures [3]. These incidents are not randomly distributed — they concentrate in under-resourced operations where workers couldn't confirm the right procedure at the right moment. The LOW-confidence escalation path is a safety equity intervention: the system refuses to answer confidently when it doesn't know, rather than producing a plausible-sounding hallucination.

**Infrastructure Governance:** The quality of public infrastructure monitoring should not depend on municipal budget size. AI-powered site inspection intelligence makes expert-level analysis accessible to jurisdictions that cannot pay for continuous human expert review.

*Machines of Loving Grace alignment:* This is precisely the use case — AI augmenting human capability in physical, consequential work, for the people the AI market has largely ignored.

---

## Competitive Positioning

| Alternative | What They Do | Where They Fall Short |
|---|---|---|
| ServiceMax | Enterprise FSM — asset management, work orders, scheduling | Custom enterprise pricing; requires Salesforce; designed for ops management, not the technician in the field [6] |
| Google / generic search | General information retrieval | Cannot synthesize multi-source, site-specific answers |
| Standard RAG chatbots | Semantic similarity retrieval | Miss temporal, causal, and entity reasoning; hallucinate when uncertain |
| Manufacturer support lines | Equipment-specific support | Not 24/7; doesn't know the site's service history |
| Physical binders / PDFs | Static documentation | Not searchable; out of date; not in the tech's hand |

**Three durable differentiators:**
1. **Site-first UX** — spatial entry point, not a chat box
2. **Multi-graph reasoning** — temporal, causal, entity, semantic; not just cosine similarity
3. **Explicit non-answer** — the only system designed to escalate rather than hallucinate

---

## Dashboard Design

The UI is organized around the three experiences, in order.

**Experience 1 — See the Site (`pages/4_Site_Map.py`)**
Zone-level map, color-coded by anomaly severity and inspection confidence. Click a zone to query the agent. This is the first thing a user sees — not a text input field.

**Experience 2 — Understand the Risk (agent pages + Knowledge Gap Map)**
- Pipeline Trace Panel: classify → graph route → retrieve → score → route → answer, step by step
- Knowledge Gap Map: zones by confidence tier — red zones are knowledge gaps, not just anomaly hotspots
- PARTIAL response view: two conflicting sources displayed side-by-side

**Experience 3 — Know Whether to Trust (`pages/5_Memory_Graph.py` + confidence badge)**
- HIGH / PARTIAL / LOW badge on every response
- MAGMA Memory Graph: interactive graph showing which nodes were traversed, which edges were followed, and why — for every answer
- LOW response: what was searched, what wasn't found, recommended next action
- Domain switcher: HVAC service history ↔ Drone inspection records

---

## Success Metrics

| Metric | Target | How Measured |
|--------|--------|-------------|
| First-time fix rate improvement | +5 pp | Operator-reported vs. historical baseline |
| Safety escalations (LOW-path routing) | Traceable, non-zero | Every LOW is a hallucination prevented |
| Knowledge gaps identified | Visible in gap map | LOW-confidence zone distribution |
| Query intent routing accuracy | > 90% | Classifier eval suite |
| Ground truth coverage | > 80% HIGH or PARTIAL | eval/ground_truth.json |
| Hallucination rate | < 2% | Adversarial eval |

---

## Demo Script (5–7 Minutes)

**Open (30 sec):** "OSHA tells us that 50,000 injuries happen every year because a worker couldn't confirm the right procedure at the right moment. The information existed. It just wasn't accessible. This is Site Intelligence."

**See the site (60 sec):** Open Site Map. "You're not looking at a chat box. You're looking at the site. Zone C is flagged. Zone X has no data. Click Zone C."

**Understand the risk (90 sec):** Run the HIGH query. Pipeline Trace lights up step by step. "You can see exactly what it searched, what it found, and why it's confident. Now watch what happens when the data disagrees." Run the PARTIAL query. "Two sources conflict. The system surfaces both and asks for human judgment."

**Know whether to trust it (60 sec):** Run the LOW query on Zone X. "No data. The system doesn't guess — it tells you exactly what it searched and what to do next. That's the feature. That's why it's safe in a safety-critical context."

**Memory Graph (90 sec):** Switch to Memory Graph page. "This is how the system reasons. This WHY query doesn't just find similar documents — it traverses the causal graph: anomaly flagged, maintenance order placed, never confirmed complete, re-flagged. That's not retrieval. That's reasoning." Inspired by MAGMA (Jiang et al., arXiv:2601.03236, 2026).

**Close (30 sec):** "Site Intelligence. See the site. Understand the risk. Know whether to trust the answer."

---

## Roadmap

| Phase | What Ships | Primary Story Served |
|-------|-----------|----------------------|
| Phase 1 (done) | RAG pipeline, confidence routing, HVAC domain | Trust layer established |
| Phase 2 (current) | Drone domain, classifier, session memory, spatial filters | Site map and risk layer |
| Hackathon | Memory Graph, Impact Counter, Pipeline Trace, three-experience UX | All three experiences complete |
| Phase 3 | Real MAGMA graph backend, live deployment, longitudinal anomaly tracking | Production-grade site intelligence |

---

## References

[1] U.S. Bureau of Labor Statistics. *Heating, Air Conditioning, and Refrigeration Mechanics and Installers.* Occupational Outlook Handbook, 2024–2034. https://www.bls.gov/ooh/installation-maintenance-and-repair/heating-air-conditioning-and-refrigeration-mechanics-and-installers.htm

[2] SightCall. *52 Field Service Stats That You Need to Know.* https://sightcall.com/blog/52-field-service-stats-that-you-need-to-know/

[3] U.S. Occupational Safety and Health Administration. *Control of Hazardous Energy (Lockout/Tagout).* 29 CFR 1910.147. https://www.osha.gov/control-hazardous-energy — OSHA Fact Sheet FS-3529: https://www.osha.gov/sites/default/files/publications/OSHAFS3529.pdf

[4] CompareSoft / IBM. *What Is First-Time Fix Rate & How to Improve Your FTFR.* https://comparesoft.com/field-service-management-software/first-time-fix-rate-ftfr/ — https://www.ibm.com/think/topics/first-time-fix-rate

[5] McKinsey Global Institute. *The Social Economy: Unlocking Value and Productivity Through Social Technologies.* 2012. Cited in: ProProfs Knowledge Base. https://www.proprofskb.com/blog/workforce-spend-much-time-searching-information/

[6] SelectHub / Capterra. *ServiceMax Reviews 2026.* https://www.selecthub.com/p/field-service-software/servicemax/ — https://www.capterra.com/p/107954/ServiceMax/

[7] HVACCareerNow. *How Much Does HVAC Training Cost?* https://hvaccareernow.com/articles/the-cost-of-hvac-training-programs — SkillCat. *HVAC Training Cost in 2026.* https://www.skillcatapp.com/post/hvac-training-cost-tuition-tools-fees

[8] SightCall. *How to Reduce Truck Rolls.* https://sightcall.com/blog/how-to-reduce-truck-rolls/ — CareAR. *What is a truck roll and how to reduce them?* https://carear.com/blog/what-is-a-truck-roll-how-to-reduce-them/ — Blitzz. *The True Cost of Truck Rolls.* https://blitzz.co/blog/what-is-truck-roll-the-true-cost-of-truck-rolls-for-businesses

[9] MarketsandMarkets. *Drone Inspection and Monitoring Market Size, Share & Trends Analysis.* https://www.marketsandmarkets.com/Market-Reports/drone-inspection-monitoring-market-99915267.html — Hammer Missions. *Why Infrastructure & Energy Are Driving the Next Phase of Drone Inspection Market Growth.* https://www.hammermissions.com/post/next-phase-of-drone-inspection-market-growth

[10] Jiang, Dongming; Li, Yi; Li, Guanpeng; Li, Bingzhe. *MAGMA: A Multi-Graph based Agentic Memory Architecture for AI Agents.* arXiv:2601.03236v1 [cs.AI], 6 January 2026. https://arxiv.org/abs/2601.03236
