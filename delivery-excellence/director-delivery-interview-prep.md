# Director of Delivery Management — Interview Preparation

---

## 1. Introduction Script — "Tell Me About Yourself"

> I'm Srikanth Akella — a delivery and program management leader with 19 years of experience leading data-intensive technology programs across BFSI, FMCG, retail, and manufacturing.
>
> I currently serve as Senior Director at LTM, where I built and lead the Data & Insights Centre of Excellence. I govern a portfolio of 6–8 concurrent data programs — cloud platform builds, enterprise data migrations, analytics activation, and increasingly AI/ML-enabled delivery — leading through 3 layers (Senior PMs → Leads → engineers/analysts) across 50–100+ people for Fortune 500 clients. Portfolio scale ranges from $5M to $25M, and I hold direct budget and margin accountability across the portfolio, not just schedule ownership.
>
> Three things define how I deliver. First, I'm a governance-first leader — I institutionalized stage-gate reviews with data quality thresholds that reduced our average delivery cycle time by 22%. Second, I bridge the technical-business gap fluently — my background started in architecture at BipSum Software, progressed through MarTech analytics delivery at Xerago E-Biz for Tier-1 banks, and now spans cloud data platforms, CDPs, and agentic AI. I don't just manage schedules; I understand what the engineers are building and why the business cares. Third, I invest in people — I've coached 4 Senior PMs into Principal and Director-track roles over the last 3 years, and I've built reusable delivery playbooks that scale our practice beyond any single engagement.
>
> I'm looking at this Director of Delivery role because it aligns with what I do best — governing complex data program portfolios, standing up delivery organizations, and driving measurable business outcomes across multi-program landscapes.

**Timing:** ~90 seconds. Adjust by dropping the Xerago bridge sentence for shorter versions.

**Anchor competencies surfaced:** Portfolio Governance, Stakeholder Management, Capacity & Talent Strategy, Data Platform Delivery, Continuous Improvement.

---

## 2. STAR Stories

### 10 Core Competencies (Reference Index)

| # | Competency | Stories Covering It |
|---|---|---|
| 1 | Portfolio Governance & Stage-Gate Management | Story A, Story D |
| 2 | Multi-Program Risk & Dependency Management | Story A, Story B |
| 3 | Data Platform & Migration Delivery | Story A |
| 4 | Agile / SAFe / Hybrid Methodology Tailoring | Story B, Story C |
| 5 | Stakeholder & Client Executive Management | Story A, Story B, Story C |
| 6 | Capacity Planning & Talent Strategy | Story B, Story D |
| 7 | EVM & Quantitative Schedule Risk Analysis | Story A, Story C |
| 8 | Vendor & SOW Management (T&M, Fixed-Price) | Story B |
| 9 | Data Quality & Governance Frameworks | Story A, Story C |
| 10 | GenAI & AI Agent Program Delivery | Story D |

---

### Story A: Data Migration & Platform Build — Global Beauty Retailer (DDX Platform)

**Competencies:** Portfolio Governance (#1), Risk & Dependency (#2), Data Platform & Migration (#3), Stakeholder Management (#5), EVM (#7), Data Quality (#9)

**Situation:**
LTM was engaged to build a multi-brand data platform for the largest global beauty retailer — unifying 40M+ customer profiles across 12 consumer brands on AWS + Databricks + Reltio. Three workstreams ran in parallel: identity resolution design, ML segmentation pipeline build, and paid-media activation connector delivery. Each brand had independent data ownership, conflicting schemas, and different definitions of "customer."

**Task:**
I was accountable for the program outcome — governing 3 workstream leads, owning the stakeholder relationships, and making the cross-workstream trade-off decisions that individual PMs don't have authority to make. My leads and PMs were responsible for execution; I was accountable for whether the platform delivered measurable activation value (audience match rates), not just a technical migration.

**Action:**

1. **Stage-gated the program** at Discovery → Data Profiling → Architecture → Build → UAT → Hypercare. The profiling gate was non-negotiable — no workstream moved to build without data quality metrics (null rates, cardinality, PII scan) signed off. This was a practice I had institutionalized across the CoE after observing that the #1 cause of mid-build scope surprises across our portfolio was skipped or shallow data profiling.

2. **Managed cross-workstream dependencies** using a dependency structure matrix (DSM). The identity resolution design was the critical path — both the ML segmentation and activation connector workstreams depended on its output schema. I sequenced delivery so identity resolution completed its schema contract 2 sprints ahead of downstream consumers.

3. **Stakeholder management (Pillar 2 — Stakeholder Engagement Assessment Matrix):** Mapped all 12 brand stakeholders on the **Unaware→Resistant→Neutral→Supportive→Leading** spectrum. Most were "Neutral" at kickoff — they didn't oppose the platform but weren't invested in it. I ran brand-specific data preview sessions (showing their actual customer data quality issues) to move them to "Supportive" before the architecture gate. This was critical because brand sign-off on the unified identity model was a hard gate — without it, we'd have built a platform nobody trusted.

4. **Negotiated phased scope** when 3 brands pushed to add additional data sources mid-build. Used the formal Change Request Log — each new source required impact analysis on timeline, budget, and downstream dependencies. Deferred 2 sources to Phase 2 to protect critical path. Communicated the trade-off to the CDO with options: "We can absorb Brand X's loyalty data now if we defer Brand Y's social signals to Phase 2. Here's the activation value comparison."

5. **Tracked CPI/SPI weekly.** When the ingestion workstream's SPI dropped to 0.88 in Sprint 4 (source-system CDC complexity was higher than assumed), I triggered the assumption log review, reassigned 2 data engineers from the activation workstream (which was ahead of schedule), and recovered SPI to 0.97 by Sprint 6.

**Result:**
Achieved 35% improvement in audience match rates at launch. All 12 brands onboarded to the unified platform. Delivered within the original timeline envelope despite mid-build scope pressure. The phased scope approach was adopted as a standard practice in the CoE for multi-brand programs.

**Commercial Accountability callout:** This program carried direct P&L (Profit & Loss) exposure — I owned the budget-to-actual variance, not just the schedule. The Phase 2 deferral decision (move 5, above) was as much a margin-protection call as a timeline call: absorbing 2 additional data sources mid-build without a change order would have eroded margin on a fixed-scope commercial arrangement. Framing the CDO conversation around activation value AND cost impact — not schedule alone — is what makes this a commercial story, not just a delivery story. Use CPI (Cost Performance Index), not just SPI, when asked directly about budget ownership.

---

### Story B: Data Modernization — Tier-1 Retail Bank Analytics & Activation (Xerago)

**Competencies:** Risk & Dependency (#2), Methodology Tailoring (#4), Stakeholder Management (#5), Capacity Planning (#6), Vendor/SOW Management (#8)

**Situation:**
At Xerago E-Biz, I led delivery of 3 concurrent analytics and activation programs for a Global Tier-1 Retail Bank. The programs spanned Salesforce Marketing Cloud, Marketo, Adobe Analytics, and DMP activation layers — each on a different contract model (2 T&M, 1 fixed-price) with different client sponsors and different MarTech vendors providing platform support.

**Task:**
Deliver all 3 programs concurrently with a 20–40 person cross-functional team, manage cross-program dependencies (shared customer data assets fed all 3 programs), and navigate the political complexity of multiple client sponsors who each believed their program was the priority.

**Action:**

1. **Hybrid methodology tailoring:** Recognized that forcing a single methodology across 3 programs with different risk profiles would fail. Implemented Kanban for ongoing marketing operations work, Scrum for feature development sprints, and waterfall gates for compliance milestones (banking regulatory sign-offs). Documented this in a reusable delivery playbook that the practice adopted across all banking engagements.

2. **Cross-program dependency management:** All 3 programs consumed the same customer behavioural data from the bank's core data warehouse. A schema change or refresh cadence change in one program could break the others. I established a shared data contract registry — any schema change required cross-program impact assessment before approval. This eliminated the 2–3 cross-program breakages per quarter we'd been experiencing.

3. **Stakeholder communication (Pillar 2 — Communications Management Plan):** Designed a tiered communication cadence: weekly sprint demos for each program team, biweekly cross-program dependency sync (I chaired this), monthly steering with all 3 client sponsors in one room. The monthly steering was initially contentious — each sponsor wanted to dominate the agenda. I restructured it with a standardized portfolio health view (RAG, key metrics, decisions needed) so each program got equal airtime and the steering focused on trade-offs, not status updates.

4. **Vendor & SOW management:** Managed the tension between the T&M and fixed-price contracts. The fixed-price program was at risk of scope creep; I enforced the change request log rigorously. For the T&M programs, I tracked utilization weekly and flagged to the client when burn rate was ahead of value delivery — building trust that we were managing their investment, not just billing hours.

5. **Capacity planning:** The 3 programs shared analytics engineers. I built a skills-based resource allocation matrix and ran monthly capacity reviews. When Program 2 needed a Marketo specialist and Program 3 had one under-utilized, I negotiated the reallocation with both sponsors — transparent, data-backed, and documented.

**Result:**
Contributed to 22% improvement in digital conversion rates across the bank's digital channels. Built reusable delivery playbooks for banking and insurance verticals adopted across the Xerago practice. Drove 30%+ practice revenue growth through follow-on engagements — earned through delivery trust, not sales.

**Honest Bridge — Rebadging / Workforce Transition:** I do not have a direct rebadging or TSA (Transition Service Agreement) story in my career history — my transitions were internal team scaling and vendor-to-vendor tool handoffs (e.g., MarTech platform operations across Salesforce Marketing Cloud, Marketo, Adobe Analytics), not formal incoming/outgoing workforce transfers. If asked directly:

> "I haven't led a formal rebadging exercise, but I have run the adjacent problem — knowledge continuity and day-1 readiness when a vendor's platform ownership changed hands mid-program at Xerago. The governing principles are the same: RACI clarity before the handoff, a documented knowledge transfer plan, and a continuity gate before the old owner exits. I'd apply that same discipline to a formal rebadging scenario — with the added rigor of contractual TSA milestones and HR/legal coordination, which I'd lean on my HR and legal partners for."

This is a **do-not-fabricate zone** — bridge honestly, then pivot to transferable principles (RACI, knowledge transfer, continuity gates) rather than claiming direct experience.

---

### Story C: Data Visualization & Analytics Governance — Cross-Portfolio Delivery Health (LTM)

**Competencies:** Methodology Tailoring (#4), Stakeholder Management (#5), EVM (#7), Data Quality & Governance (#9)

**Situation:**
Across LTM's portfolio of 6–8 concurrent data programs, executive steering meetings were running 60 minutes, dominated by status debates. Each program reported health differently — some used RAG, some used percentage complete, some used "vibes." The CTO couldn't compare programs, couldn't make resource trade-off decisions, and kept asking "what does green actually mean?"

**Task:**
Design and stand up a cross-portfolio delivery health dashboard that standardized program health reporting, gave the CTO decision-grade data, and cut steering meeting time.

**Action:**

1. **Defined a quantitative health model:** Replaced subjective RAG with a composite score: CPI/SPI (weighted 40%), pipeline SLA adherence (weighted 25%), data quality scores (weighted 20%), and team health indicators like utilization rate and sprint commitment accuracy (weighted 15%). Every program measured the same way.

2. **Built the dashboard** as a live view — not a slide deck. Updated weekly. Programs that were "green by vibes" but had SPI below 0.9 were now visibly amber. This surfaced 2 programs that needed intervention before they became critical.

3. **Stakeholder management (Pillar 2 — show-don't-tell principle):** The key insight from Pillar 2's Communications Management Plan is that data programs need live dashboards over slide decks. I applied this at the portfolio level. The CTO could now self-serve program health between steering meetings, which eliminated 80% of the ad-hoc "give me a status update" requests that were consuming PM time.

4. **Data quality governance integration:** Embedded data quality scores (completeness, accuracy, timeliness) as a first-class health metric. Programs couldn't claim "green" if their data quality SLAs were breached. This enforced accountability that data quality was a delivery concern, not just a "data team problem."

5. **Process improvement feedback loop:** Used the dashboard data to identify systemic patterns in retrospectives. Discovered that every program was underestimating data quality remediation effort by ~30%. Institutionalized a mandatory data profiling sprint in the discovery phase across all future programs — eliminating the #1 cause of mid-build scope surprises.

**Result:**
Cut executive steering meetings from 60 to 20 minutes. Eliminated "what does green mean" ambiguity. The data profiling sprint practice reduced average delivery cycle time by 22% across the portfolio. The dashboard model was adopted as an LTM delivery standard.

**BAU vs. Transformation callout:** The dashboard itself is a dual-track governance artifact — it tracked transformation programs (new platform builds) and BAU/run-the-business commitments (pipeline SLA adherence, data quality scores) on the same view, weighted separately (40% schedule, 25% SLA, 20% quality, 15% team health). This mattered because engineers were shared across BAU support and transformation build work — without visibility into both tracks, transformation pressure would silently degrade BAU SLA adherence. Resource leveling (Section 1.4 of the reference framework) was the underlying technique: protecting BAU commitments while transformation competed for the same engineers.

---

### Story D: AI/ML-Enabled Data Platform — Agentic Field Service & VoC Suite (LTM)

**Competencies:** Portfolio Governance (#1), Capacity Planning (#6), GenAI & AI Agent Program Delivery (#10)

**Situation:**
LTM was engaged on two related programs: (1) an Agentic Field Service Optimisation (FSO) platform for a leading European CV manufacturer, built on Snowflake Cortex Agents with a 4-layer architecture (Forecasting, Scheduling, Routing, Parts Intelligence); and (2) a VoC AI/ML suite with predictive maintenance scoring for a global automotive OEM, built on Databricks and embedded in service KPI dashboards used by 200+ field engineers. Both programs were among the first in our portfolio where AI/ML models were the primary deliverable, not a supporting feature.

**Task:**
Manage delivery of these AI/ML-enabled programs — ensuring that model development didn't run in an ungoverned "research mode," that business value was demonstrable at each gate, and that the team had the right skills for a delivery type our CoE hadn't done at this scale before.

**Action:**

1. **Value-first gate for AI/ML delivery:** Introduced a rule: every model required a documented business decision change before build approval. The data science team couldn't build a churn prediction model just because the data existed — they had to articulate: "This model will change how the dispatcher assigns priority. Here's the current decision process, here's how it changes, here's the expected FTFR improvement." This prevented the "model graveyard" problem — models built but never operationalized.

2. **Capacity planning for AI/ML talent:** Our CoE had strong data engineering bench but thin ML engineering capability. I ran a skills inventory and gap analysis — identified that we had 0 dedicated ML engineers but 2 programs needing model deployment and MLOps. Secured 2 ML engineers through targeted augmentation, paired each with a senior data engineer for knowledge transfer. Built a 90-day ramp plan so the augmented talent could operate independently by Sprint 6.

3. **Stage-gate adaptation for ML programs:** Traditional stage gates (Architecture → Build → UAT) don't fit ML development, where model training is iterative and "done" is a performance threshold, not a feature checklist. Adapted the gates: Discovery → Data Profiling & Feature Engineering → Model Training & Validation (with defined accuracy/F1 thresholds) → Integration & Embedding → UAT → Hypercare. Each gate had ML-specific exit criteria — model performance benchmarks, bias checks, explainability documentation.

4. **GenAI-augmented delivery practices:** Piloted GenAI-assisted risk identification across RAID logs — used LLM-based pattern matching to scan risk descriptions across all portfolio programs and surface cross-program dependency conflicts that manual review was missing. Reduced manual risk review effort by 35% while surfacing 2× more cross-program dependency conflicts.

5. **Governed the agentic architecture:** The Cortex Agents FSO platform had 4 autonomous agents that needed to coordinate. Worked with the architecture team to define agent interaction contracts, fallback behaviours, and human-in-the-loop escalation triggers. From a delivery standpoint, I treated each agent as a workstream with its own stage-gate — preventing the integration nightmare of 4 agents built independently and expected to "just work" together.

**Result:**
FSO platform achieved 15% FTFR improvement. VoC suite with predictive maintenance scoring deployed to 200+ field engineers. Both programs delivered on schedule. The value-first gate and ML-adapted stage-gate model were adopted as CoE standards for all future AI/ML programs.

**100+ Team Leadership callout:** Across the CoE portfolio (15+ engagements, 50–100+ people), I lead through 3 organizational layers, not directly: Senior PMs (direct reports) → Leads (workstream owners, like the 3 workstream leads in Story A) → engineers/analysts/data scientists (executing). Span of control at my layer is 4–6 Senior PMs; each Senior PM carries their own span of Leads. When asked about "managing 100+ people," the accurate framing is: "I lead a matrixed organization through leadership layers — I set direction and remove blockers for Senior PMs, who in turn govern Leads." Never claim flat, direct management of 100 individuals — that overstates span of control and reads as inexperience with organizational design.

---

## 3. Pillar 2 — Stakeholder & Client Management: Key Facets for Interview Reference

The following are the 5 core facets from the Stakeholder & Client Management responsibility segment (Section 1.3 of the Director Delivery reference framework). Each story above references at least one.

| Facet | PMBOK Tool | How It Shows Up in Stories |
|---|---|---|
| **Stakeholder Positioning** | Stakeholder Engagement Assessment Matrix | Story A — mapped 12 brand stakeholders on Unaware→Leading spectrum; ran data preview sessions to move them from Neutral to Supportive before architecture gate. |
| **Tiered Communication** | Communications Management Plan | Story B — weekly sprint demos, biweekly dependency syncs, monthly steering with standardized portfolio health view. Story C — replaced slide decks with live dashboards (show-don't-tell). |
| **Ownership Clarity** | RACI Chart | Story A — data quality ownership (source system owner vs. data engineering) settled via RACI. Story C — embedded data quality as a delivery-owned metric, not a "data team problem." |
| **Scope Discipline** | Change Request Log | Story A — formalized every new data source as a change request with impact analysis; deferred 2 sources to Phase 2 to protect critical path. |
| **Institutional Learning** | Lessons Learned Register | Story C — cross-program retros revealed systemic data profiling underestimation; institutionalized profiling sprint as standard practice. |

---

## 4. Skills & Technologies — Quick Reference for Interview Anchoring

### Delivery & Governance

| Skill | Evidence Point |
|---|---|
| Portfolio governance ($5M–$25M) | LTM: 6–8 concurrent programs, 15+ engagements |
| Stage-gate management | Mandatory data profiling gates → 22% cycle time reduction |
| EVM (CPI/SPI) | Weekly tracking; SPI recovery from 0.88→0.97 in DDX |
| Risk management (Monte Carlo, DSM) | Cross-program dependency matrix, probabilistic scheduling on 40+ source migrations |
| Hybrid methodology (Agile/SAFe/Waterfall) | Xerago banking: Kanban + Scrum + waterfall gates |
| Vendor/SOW management | T&M + fixed-price mixed portfolio at Xerago |
| P&L / commercial ownership | Budget-to-actual variance owner across $5M–$25M portfolio; CPI tracked alongside SPI |
| Productivity / cost-out | $1.2M annual savings via multi-cloud consolidation; 75–85% utilization target discipline; 35% reduction in manual risk review effort via GenAI-assisted RAID review |

### Data Platforms & Technologies

| Category | Technologies |
|---|---|
| Cloud Platforms | AWS, Google Cloud Platform, Microsoft Azure |
| Data Platforms | Snowflake, Databricks (Unity Catalog, Delta Lake), BigQuery |
| Modern Data Stack | dbt Cloud, Apache Airflow, Kafka |
| CDP | Reltio, Composable CDP on Snowflake, Salesforce Data Cloud |
| AI/ML Delivery | Snowflake Cortex Agents, Databricks Mosaic AI, MLflow, Kubeflow |
| GenAI Orchestration | LangGraph, LangChain, RAG pipelines, LLM observability (Langfuse) |
| Governance | Collibra, Alation, Atlan, Great Expectations |
| PPM Tools | Jira Align, Planview, ServiceNow SPM, Smartsheet |

### Soft Skills — Interview Signals

| Competency | Signal to Project |
|---|---|
| Executive communication | Steering meetings cut 60→20 min; options-not-status framing |
| People development | 4 PMs promoted to Principal/Director track in 3 years |
| Client trust → revenue | 30%+ practice revenue growth at Xerago through delivery trust |
| Cross-cultural delivery | 12-brand global program; 30+ market delivery at AECC; 3 time zones at BipSum |
| Data literacy | Statistics MSc; architecture background; can challenge data modeling decisions |

---

## 5. Behavioural Question Quick-Reference

| Common Question | Story to Use | Key Talking Point |
|---|---|---|
| "Tell me about a complex program you led" | Story A (DDX) | 40M+ profiles, 12 brands, phased scope negotiation |
| "How do you manage stakeholders with competing priorities?" | Story B (Banking) | 3 sponsors, tiered comms, standardized steering |
| "How do you drive continuous improvement?" | Story C (Dashboard) | Systemic pattern → profiling sprint → 22% cycle time reduction |
| "Experience with AI/ML delivery?" | Story D (FSO + VoC) | Value-first gate, ML-adapted stage gates, agentic architecture governance |
| "How do you handle scope creep?" | Story A (DDX) | Change request log, impact analysis, CDO trade-off framing |
| "How do you manage team capacity?" | Story D (FSO + VoC) | Skills gap analysis, targeted augmentation, 90-day ramp plan |
| "How do you handle underperforming programs?" | Story C (Dashboard) | Quantitative health model exposed "green by vibes" programs |
| "How do you tailor methodology?" | Story B (Banking) | Kanban + Scrum + waterfall gates per risk profile |
| "How do you ensure data quality?" | Story A + C | Profiling gates, quality as delivery metric, not "data team problem" |
| "How do you develop your team?" | Intro + Story D | 4 PM promotions, knowledge transfer pairing, reusable playbooks |
| "What's your budget/P&L accountability?" | Story A (Commercial callout) | CPI ownership, margin-protection framing on scope deferral |
| "Have you led a rebadging/transition program?" | Story B (Honest Bridge) | No direct experience — bridge to RACI + knowledge transfer principles, do not fabricate |
| "How do you balance BAU and transformation?" | Story C (BAU callout) | Dual-track dashboard, resource leveling across shared engineers |
| "How large a team have you led?" | Intro + Story D (100+ callout) | 3 leadership layers, 4–6 Senior PM span of control, not flat 100-person management |
| "How do you drive productivity/cost-out?" | Skills table | $1.2M multi-cloud consolidation, 75–85% utilization discipline, GenAI-assisted risk review |

---

## 6. Seniority Gap Coverage — Verification Map

Added to close 5 gaps flagged against the JD (Job Description): P&L ownership, rebadging/transition, BAU vs. transformation, 100+ team leadership, productivity. Status per gap:

| Gap | Coverage Added | Evidence Strength |
|---|---|---|
| P&L / commercial ownership | Story A — Commercial Accountability callout | Verifiable — extends existing CPI/SPI evidence |
| Rebadging / transition | Story B — Honest Bridge statement | **No direct evidence — bridge only, flagged do-not-fabricate** |
| BAU vs. transformation | Story C — BAU vs. Transformation callout | Verifiable — extends existing dashboard weighting model |
| 100+ team leadership | Story D + Intro — leadership layers, span of control | Verifiable — reframes existing headcount, no new claims |
| Productivity / cost-out | Skills table — new row | Verifiable — reuses $1.2M and utilization stats already in career data |

**Critic note:** 4 of 5 gaps closed with verifiable extensions of existing evidence. Rebadging remains a genuine gap — the honest bridge is the correct move, not a workaround. If the interviewer probes hard on rebadging specifically, redirect to the transferable governance principles and be prepared to say plainly: "That's not a program type I've led directly."

---

## 7. Full Constitution — Delivery Portfolio Health Dashboard

This is the decision-intelligence breakdown of the dashboard referenced in Story C. Use this when an interviewer asks "how exactly did you measure program health" — most candidates say "RAG status"; this section is what separates a Director-level answer from a PM-level one.

### 7.1 Why SPI + CPI Alone Is Insufficient

SPI (Schedule Performance Index) and CPI (Cost Performance Index) diagnose **schedule and cost health** — they answer "are we on time and on budget." They do not answer "is the program actually healthy." A program can carry SPI/CPI near 1.0 while quietly accumulating data quality debt or burning out its team — both of which surface as schedule/cost problems only *after* the damage is done. This was the exact failure mode the dashboard was built to close: programs that were "green by vibes" on schedule but had underlying risk invisible to SPI/CPI alone.

### 7.2 Full Constitution — Delivery Portfolio Health Dashboard

| Component | Weight | What It Measures | Criterion Served | Failure Mode It Catches |
|---|---|---|---|---|
| **CPI/SPI** | 40% | Cost and schedule variance against baseline | Diagnostic completeness — the quantitative core | Budget overrun, schedule slippage |
| **Pipeline SLA Adherence** | 25% | Operational reliability of data pipelines (uptime, latency, failure rate against agreed SLA) | Actionability — tells you where to intervene operationally | Pipelines technically "on schedule" but operationally unstable |
| **Data Quality Scores** | 20% | Completeness, accuracy, timeliness of data at each stage | False-positive risk — the metric that exposed "green by vibes" programs | Schedule/cost look fine, but the data underneath is unreliable — the #1 hidden risk in data programs |
| **Team Health** | 15% | Utilization rate (target 75–85%) and sprint commitment accuracy | Stakeholder trust and sustainability — protects against burnout-driven collapse | A program hitting dates by over-extending the team — a short-term win that produces a mid-term failure |

### 7.3 Why This Weighting, Not Equal Weighting

Equal-weighting (25% each) was considered and rejected. Reasoning: schedule/cost variance is the highest-frequency, most decision-relevant signal for portfolio-level trade-offs (the CTO's primary question is "will this land on time and on budget"), so it anchors the model at 40%. Data quality and team health are lower-frequency but higher-severity when triggered — hence meaningful weight (20% and 15%) rather than a token allocation. Equal-weighting would have diluted the schedule/cost signal and made the composite score less decision-useful, defeating the purpose of replacing subjective RAG with something a CTO could act on directly.

### 7.4 Mechanism, Not Metric

The dashboard's value wasn't the four numbers — it was the **institutional mechanism** around them:

1. **Live, not static.** Updated weekly, self-serve by the CTO — not a slide deck refreshed for steering meetings. This eliminated 80% of ad-hoc "give me a status update" requests.
2. **Composite score forces reconciliation.** A program can't claim "green" on schedule while quietly failing data quality — the composite score surfaces the conflict automatically, rather than relying on a PM to self-report it.
3. **Retrospective feedback loop.** Dashboard data was pattern-matched across the portfolio in quarterly retros — this is how the 30% data profiling underestimation was discovered, which led to the mandatory profiling gate (Story A/C), which in turn drove the 22% cycle time reduction. The dashboard didn't just report health — it generated the next institutional fix.

### 7.5 The Full Causal Chain (Memorize This Sequence)

```
Subjective RAG reporting (inconsistent, no comparability across programs)
        ↓ problem: CTO can't compare programs or make trade-off calls
Weighted composite health model (CPI/SPI 40% + SLA 25% + quality 20% + team 15%)
        ↓ mechanism: live dashboard, self-serve, weekly refresh
Steering meeting time: 60 min → 20 min
        ↓ side effect: retrospective pattern-matching on the dashboard data
Discovery: every program underestimates data profiling effort by ~30%
        ↓ institutional response: mandatory profiling gate before Build phase
Average delivery cycle time: reduced by 22% portfolio-wide
```

**Interview delivery note:** If asked to "explain your dashboard," do not lead with the weights — lead with the failure mode it fixed ("programs were green by vibes"), then the four components, then the causal chain to the profiling gate. The weights are a follow-up-question answer, not an opening answer — leading with numbers before the problem sounds rehearsed rather than reasoned.

# Q: You clearly have a strong technical background — why not stay an architect?
- A: it's true, I started my career in technical role and eventually moved into delivery as I found most of the projects won't fail because of the architechture, but due to lack of governance, capacity building and stakeholder trust. Hence I made a delibrate choice to be part of the delivery and stay technically fluent enough to govern AI/ML and data platform delivery, not build it.

