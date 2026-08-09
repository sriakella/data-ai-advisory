# Director of Delivery Management — Data Project & Program Management

---

## 1. Project Management Body of Knowledge (PMBOK) Techniques Mapped to Each Responsibility Segment

Context: PMBOK 7th Edition (principle-based) + select 6th Edition process tools. Scoped to **data project management** — data platforms, analytics, Machine Learning / Artificial Intelligence (ML/AI), Customer Data Platforms (CDPs), data migrations, and governance programs.

---

### 1.1 Portfolio Execution Governance

| PMBOK Technique | Application in Data Delivery |
|---|---|
| **Benefits Realization Management** | Track whether a data platform investment actually reduced time-to-insight or improved model accuracy — not just "delivered on time." |
| **Stage-Gate Reviews** | Gate data programs at: Discovery → Data Profiling → Architecture → Build → User Acceptance Testing (UAT) → Hypercare. No gate pass without data quality metrics cleared. |
| **Earned Value Management (EVM)** | Cost Performance Index / Schedule Performance Index (CPI/SPI) on data migration sprints — catches velocity decay early when source-system complexity surprises the team. |
| **Program Roadmap & Milestone Planning** | Sequence workstreams (ingestion → transformation → activation → reporting) with hard dependencies visible to the portfolio board. |
| **Governance Framework (PMBOK 7 — Stewardship)** | Define Responsible, Accountable, Consulted, Informed (RACI) across data engineering, analytics, platform ops, and business stakeholders. Enforce decision rights at the portfolio level. |

**Key Artifacts:** Portfolio Health Dashboard (live view of all program Red-Amber-Green (RAG) status, CPI/SPI, milestone burn-up — updated weekly, consumed by steering committee and Chief Technology Officer / Chief Data Officer (CTO/CDO)), Stage-Gate Checklist (per-gate entry/exit criteria including data quality thresholds, architecture sign-off, infra provisioning confirmation — prevents premature phase transitions), Program Roadmap (sequenced workstream timeline with hard dependencies, resource allocations, and critical path highlighted — the single artifact the portfolio board uses for trade-off decisions), Benefits Realization Tracker (maps each program to its business case Key Performance Indicators (KPIs) — time-to-insight reduction, model accuracy targets, cost avoidance — and tracks actuals vs. projected post-delivery).

---

### 1.2 Risk & Dependency Management

| PMBOK Technique | Application in Data Delivery |
|---|---|
| **Risk Register + Probability-Impact Matrix** | Maintain a living register for data programs. Top risks: source-system schema drift, Personally Identifiable Information (PII) exposure, vendor Application Programming Interface (API) deprecation, model drift post-deployment. |
| **Monte Carlo Simulation (Schedule Risk)** | Run probabilistic schedule analysis on large data migrations — deterministic timelines lie when 40+ source systems are in play. |
| **Dependency Structure Matrix (DSM)** | Map cross-program data lineage dependencies. A change in the ingestion layer for Program A may break transformations in Program B. |
| **Issue Log + Escalation Path** | Structured escalation: team lead (24h) → Director (48h) → Vice President (VP)/Sponsor (72h). Data-specific: data quality Service Level Agreement (SLA) breaches auto-escalate. |
| **Assumption Log** | Document assumptions about data volumes, refresh cadence, API rate limits. Review monthly — stale assumptions are the #1 silent risk in data programs. |

**Key Artifacts:** Risk Register (living document with quantified probability × impact scoring per risk, owner assignment, mitigation actions, and review cadence — updated bi-weekly, escalated to steering when residual risk exceeds appetite), Dependency Map / DSM (visual cross-program dependency matrix showing data lineage connections — which program's output feeds another's input — reviewed at every portfolio sync to catch cascade failures), Assumption Log (catalogues every assumption about data volumes, API rate limits, source-system availability, refresh windows — each with an expiry date and validation trigger), Issue Log with Escalation Matrix (structured escalation tiers with SLA: team lead 24h → Director 48h → VP/Sponsor 72h, auto-escalation rules for data quality SLA breaches).

---

### 1.3 Stakeholder & Client Management

| PMBOK Technique | Application in Data Delivery |
|---|---|
| **Stakeholder Engagement Assessment Matrix** | Map each stakeholder (CDO, VP Analytics, Data Engineering Lead, Business Owner) on Unaware → Resistant → Neutral → Supportive → Leading. Target state per phase. |
| **Communications Management Plan** | Cadence: weekly sprint demos (data team), biweekly steering (sponsors), monthly portfolio review (exec). Data programs need show-don't-tell — live dashboards over slide decks. |
| **RACI Chart** | Critical for data programs where ownership blurs: who owns data quality — source system owner or data engineering? RACI settles it. |
| **Lessons Learned Register** | Capture and circulate across programs. Pattern: "We underestimated Change Data Capture (CDC) complexity on legacy Enterprise Resource Planning (ERP)" saves the next team 4 weeks. |
| **Change Request Log** | Scope changes in data programs often masquerade as "just add one more source." Formalize every new data source as a change request with impact analysis. |

**Key Artifacts:** Communications Management Plan (defines cadence, format, audience, and channel for every stakeholder tier — exec steering deck vs. sprint demo vs. business user changelog — prevents ad-hoc status requests from consuming delivery time), Stakeholder Engagement Assessment Matrix (maps each stakeholder on the Unaware→Leading spectrum with current state and target state per phase — the Director reviews this before every steering meeting to calibrate messaging), RACI Chart (single-page ownership map for contentious boundaries: who owns data quality, who approves schema changes, who signs off on go-live — resolves the ambiguity that kills data programs), Change Request Log (formal intake for every new data source, schema change, or scope addition — includes impact analysis on timeline, budget, and downstream dependencies — the artifact that prevents "just one more source" from derailing the program), Lessons Learned Register (cross-program pattern library — each entry has: what happened, root cause, what we changed systemically — circulated quarterly so Program N+1 doesn't repeat Program N's mistakes).

---

### 1.4 Team & Capacity Leadership

| PMBOK Technique | Application in Data Delivery |
|---|---|
| **Resource Breakdown Structure (RBS)** | Categorize capacity by skill: data engineers, analytics engineers, ML engineers, platform/DevOps, data stewards. Scarce skills (ML, platform) are the bottleneck — manage them first. |
| **RACI + Responsibility Assignment Matrix** | Assign clear ownership per workstream. In data programs, the "shared resource" anti-pattern kills velocity — name owners. |
| **Team Charter** | Define working agreements: Pull Request (PR) review SLAs, on-call rotation for data pipelines, definition of done (includes data quality checks, not just code merge). |
| **Tuckman Model (Team Development)** | New data teams hit "storming" around data modeling conventions and tooling choices. Anticipate it; facilitate the decision, don't let it fester. |
| **Resource Leveling / Smoothing** | Balance sprint loading across data engineers. Data programs are spiky — ingestion sprints are engineer-heavy, activation sprints are analyst-heavy. Level across phases. |

**Key Artifacts:** Capacity Model / Resource Allocation Matrix (who is allocated where, at what %, with what skills — updated monthly, used to make hire/augment/defer decisions and to defend resource requests to leadership), Team Charter (working agreements per program team: PR review SLAs, on-call rotation, definition of done including data quality checks — signed by all team members at kickoff, revisited at retrospectives), Skills Inventory & Gap Analysis (maps current team capabilities against program demand — surfaces critical gaps like "we have 0 ML engineers but 2 programs need model deployment" — drives hiring and training priorities), Utilization & Health Report (tracks utilization targets 75–85%, flags individuals above 95% for burnout risk — the artifact that prevented Story 3's team collapse).

---

### 1.5 Process & Continuous Improvement

| PMBOK Technique | Application in Data Delivery |
|---|---|
| **Retrospectives (Agile Practice Guide)** | Run every sprint, but also run cross-program retros quarterly. Pattern-match systemic issues: "Every program underestimates data quality remediation by 30%." |
| **Process Tailoring (PMBOK 7)** | Don't force pure Scrum on a data migration. Hybrid works: Kanban for pipeline ops, Scrum for feature development, waterfall gates for compliance milestones. |
| **Control Charts / Statistical Process Control (SPC)** | Monitor pipeline reliability, build times, test pass rates. A control chart on data pipeline SLA adherence is more useful than a status slide. |
| **Root Cause Analysis (Ishikawa / 5 Whys)** | When a data pipeline fails in production, don't patch — trace to root. Often: missing schema validation at ingestion, not a code bug. |
| **OPAs (Organizational Process Assets)** | Build and maintain a delivery playbook: templates for data program kickoff, data profiling checklists, cutover runbooks, hypercare dashboards. Codify what works. |

**Key Artifacts:** Delivery Playbook (the meta-artifact — contains all templates, checklists, runbooks, and standards that codify "how we deliver data programs here" — versioned, maintained by the Director, consumed by every program team), Retrospective Action Register (not just retro notes — a tracked backlog of systemic improvements with owners and due dates, reviewed quarterly to confirm changes actually landed), Process Metrics Dashboard (control charts on pipeline reliability SLA, sprint cycle time, defect escape rate, lead time — the Director uses this to prove "we're getting faster" or diagnose "where we're slowing down"), Data Profiling Checklist (standardized pre-build checklist: source completeness, null rates, cardinality, PII scan, schema stability — the artifact born from the lesson that every program underestimates data profiling by 30%), Cutover Runbook (step-by-step migration cutover plan with rollback triggers, communication scripts, and war-room roles — the difference between a controlled go-live and a fire drill).

---

## 2. Ladder Explanations — Each Responsibility

### 2.1 Portfolio Execution Governance

**ELI10 — Simple:**
Imagine you're running 5 science projects at once for a school fair. You need a big board showing which project is on track, which is behind, and which needs more supplies. That's portfolio governance — keeping all projects visible so none of them surprise you on fair day.

**Practitioner:**
You operate a portfolio Kanban or Project Portfolio Management (PPM) tool (Jira Align, Planview, ServiceNow Strategic Portfolio Management (SPM)) with real-time health indicators per program. You enforce stage gates — a data platform build doesn't move from "Architecture" to "Build" without a signed-off data model and confirmed infrastructure provisioning. You run biweekly portfolio syncs where program leads report CPI/SPI, and you make trade-off decisions: if Program A needs 2 more data engineers, which program yields them?

**Director / Architect:**
Portfolio governance at Director level is resource allocation under constraint and strategic sequencing. You decide *which* data programs run and in what order — driven by business value (revenue impact, regulatory deadline, cost avoidance), not by who lobbied loudest. You own the portfolio-level risk appetite: are we running too many concurrent migrations with shared infrastructure? You present portfolio health to the CTO/CDO with options, not just status — "We can accelerate the Customer Data Platform (CDP) build by 6 weeks if we defer the reporting modernization to Q3. Here's the Net Present Value (NPV) trade-off."

---

### 2.2 Risk & Dependency Management

**ELI10 — Simple:**
Before a road trip, you check the weather, make sure the car has gas, and pack a spare tire. Risk management is doing that for projects — figuring out what could go wrong and having a plan before it does.

**Practitioner:**
You maintain a risk register with quantified probability and impact (not just red/amber/green theater). For data programs, you track specific risk categories: data quality (source completeness, accuracy), technical (API limits, schema changes), regulatory (PII handling, consent), and organizational (sponsor change, competing priorities). You run dependency mapping sessions across programs — because in data, everything is connected: a change to the customer ID resolution logic in one program breaks downstream in three others.

**Director / Architect:**
At Director level, risk management is portfolio-level pattern recognition. You're not tracking 200 individual risks — you're identifying systemic risk patterns: "We consistently underestimate data profiling effort by 40%," so you institutionalize a profiling spike in every program's discovery phase. You own the escalation culture — teams that hide risks get coached, not punished. You present the risk portfolio to leadership as a decision matrix: "These 3 risks require your decision. These 12 I've already mitigated. Here's what I need from you."

---

### 2.3 Stakeholder & Client Management

**ELI10 — Simple:**
You know how in a group project, some people care about the design, some about the writing, and the teacher cares about the deadline? Stakeholder management is figuring out what each person cares about and making sure they all feel heard — so nobody torpedoes the project at the last minute.

**Practitioner:**
You segment stakeholders by influence and interest. The CDO wants strategic alignment. The VP of Analytics wants self-service dashboards yesterday. The data engineering lead wants architectural integrity. The business sponsor wants their report. You tailor communication — execs get a 1-page dashboard, technical leads get a sprint demo, business users get a "what changed this week" email. You manage expectations explicitly: "The data will be 85% accurate by go-live; 95% by week 4 of hypercare. Here's why."

**Director / Architect:**
At Director level, stakeholder management is political navigation with integrity. You build trust capital with the C-suite so when you need to deliver hard news — "This migration will take 4 months longer than the original estimate because we discovered 12 undocumented source systems" — they trust your judgment and don't shoot the messenger. You coach your program managers on stakeholder strategy, not just status reporting. You own the client relationship in consulting contexts: renewals, expansions, and escalation resolution.

---

### 2.4 Team & Capacity Leadership

**ELI10 — Simple:**
You're the coach of several sports teams at once. You decide who plays on which team, make sure nobody is exhausted, and help the assistant coaches get better at their jobs.

**Practitioner:**
You maintain a capacity model — who's allocated where, at what percentage, with what skills. In data programs, you manage a talent mix: data engineers (pipeline builders), analytics engineers (data build tool (dbt)/transformation), ML engineers (model developers), data stewards (quality), and platform engineers (infra). You track utilization targets (75-85%) and watch for burnout signals. You run 1:1s with delivery leads and senior Individual Contributors (ICs), unblocking them and coaching them on both technical and delivery skills.

**Director / Architect:**
At Director level, team leadership is org design and talent strategy. You're deciding: do we build a platform team or embed platform engineers in every program? (Answer: platform team with embedded liaisons.) You're hiring, growing, and retaining senior data talent in a competitive market. You balance the "build vs. buy vs. partner" decision for capacity — staff augmentation for peak loads, full-time for core capabilities. You create career paths that keep senior engineers from leaving: technical fellow tracks, architecture rotations, conference sponsorship.

---

### 2.5 Process & Continuous Improvement

**ELI10 — Simple:**
After every game, the coach watches the replay to see what went right and what went wrong, then changes the plays for next time. That's continuous improvement — making the team better every week, not just winning one game.

**Practitioner:**
You run sprint retros and program-level retrospectives. But the real work is turning retro outputs into systemic changes: if three programs report "data quality issues discovered too late," you don't just note it — you add a mandatory data profiling sprint to the delivery framework. You maintain a delivery playbook with templates, checklists, and runbooks. You track process metrics: lead time, cycle time, defect escape rate, pipeline reliability SLA.

**Director / Architect:**
At Director level, continuous improvement is building a learning organization. You establish a delivery community of practice across programs. You commission quarterly delivery health assessments — not just "are projects on time" but "are we getting faster, more predictable, and producing higher quality?" You benchmark against industry: "Our data migration cycle time is 14 weeks; best-in-class is 10. Here's the 3-initiative improvement plan to close the gap." You evolve the delivery framework itself — maybe you're moving from Scaled Agile Framework (SAFe) to a lighter-weight flow-based model because your data programs don't fit the Program Increment (PI) planning cadence.

---

## 3. ELI14 — The Entire Role in Plain Language + 3 Stories

### What Does a Director of Delivery Management Actually Do?

Think of a Director of Delivery Management as the person who makes sure big, complicated technology projects actually get finished — on time, without blowing the budget, and without the team burning out. They don't write the code or build the data pipelines themselves. Instead, they run the *system* that lets dozens of engineers and analysts do their best work across multiple projects at once.

They're responsible for three things: **the work gets done** (projects deliver), **the people are healthy** (teams aren't overloaded), and **the organization gets smarter** (next project runs better than the last one).

### Story 1: "The Migration That Almost Ate the Quarter"

A company needed to move 200 million customer records from an old database to a new cloud platform. Three teams were working on it. Two weeks before the deadline, the Director discovered that Team B's work depended on a data format that Team A hadn't finished building yet — and nobody had flagged it because each team was tracking their own progress separately. The Director pulled all three leads into a room, drew the dependency map on a whiteboard, re-sequenced the work, and moved two engineers from Team C (which was ahead of schedule) to Team A for a week. The migration shipped on time. Without the Director, each team would've been "green" in their own status report while the whole program was silently failing.

### Story 2: "The Dashboard Nobody Asked For"

After delivering a data analytics platform, the Director noticed that every program in the portfolio kept having the same argument: "Is the project on track or not?" — because everyone measured "on track" differently. She built a standardized delivery health dashboard — same metrics, same definitions, same red/amber/green thresholds — and made it the single source of truth for the executive steering committee. Within two quarters, status meetings dropped from 60 minutes to 20 minutes, and executives stopped asking "but what does green really mean?" That's continuous improvement: she didn't just run projects, she fixed the system that runs projects.

### Story 3: "The Team That Was Too Busy to Succeed"

A data engineering team was allocated to four projects simultaneously. Every engineer was at 110% utilization. Sprint velocity was dropping every week, bugs were increasing, and two senior engineers quietly updated their LinkedIn profiles. The Director pulled utilization data, showed the VP that the team was structurally overloaded (not underperforming), and made the case to defer one project by 6 weeks and bring in two contract engineers for another. Within a month, velocity recovered, bugs dropped, and both senior engineers stayed. The Director's job isn't to push harder — it's to make it structurally possible for the team to succeed.

---

## 4. Required Skills & Experience — Director-Level Resume

*Pareto lens: the 20% of skills that drive 80% of Director-level impact are in Tier 1. Tier 2 skills differentiate strong candidates. Tier 3 skills are valuable but delegatable or acquirable on the job.*

### Tier 1 — The 20% That Drives 80% of Impact

| Skill | Type | Why It's Tier 1 |
|---|---|---|
| **Executive Communication** | Soft | The single highest-leverage skill. Every other skill is invisible to leadership without this one. Translate "pipeline SLA breach" into "revenue reporting delayed 48h — here are options." |
| **Portfolio Governance & Prioritization** | Hard | Deciding *what runs and in what order* across 5–10 programs is the Director's irreplaceable function. Weighted Shortest Job First (WSJF), Must have / Should have / Could have / Won't have (MoSCoW), value-effort matrix. |
| **Data Domain Expertise** | Hard | Credibility with data architects and ML engineers. You don't build pipelines — but you must know that a CDC migration is fundamentally different from a batch Extract-Transform-Load (ETL) lift, and staff/schedule accordingly. Platforms: Snowflake, Databricks, BigQuery. Patterns: ETL / Extract-Load-Transform (ELT), CDPs, Master Data Management (MDM), ML lifecycle. |
| **Decision-Making Under Ambiguity** | Soft | Data programs are inherently uncertain (unknown source quality, evolving requirements, model drift). Directors who wait for 100% information deliver late. Decide at 70%, course-correct fast. |
| **Capacity & Talent Strategy** | Soft | The constraint in every data org is people, not technology. Knowing when to hire, when to augment, and when to defer a program is the difference between a functioning org and a burnout factory. |

### Tier 2 — Differentiators That Separate Strong From Average

| Skill | Type | Why It's Tier 2 |
|---|---|---|
| **Risk Management (Quantitative)** | Hard | Monte Carlo scheduling, dependency mapping, Risks-Assumptions-Issues-Dependencies (RAID) logs. Moves risk from "red/amber/green theater" to probabilistic decision-making. |
| **Financial Management** | Hard | Program budgeting ($5M–$50M+), forecasting, vendor Statement of Work (SOW) management, capital expenditure (capex) vs. operational expenditure (opex) classification. You can't govern a portfolio without owning the numbers. |
| **Organizational Influence** | Soft | Secure resources, shift priorities, and drive change without direct authority over every team. Political navigation with integrity. |
| **Coaching & Mentorship** | Soft | Your leverage is through the delivery leads you develop. A Director who can't grow PMs into Senior PMs becomes a bottleneck. |
| **Delivery Methodology Tailoring** | Hard | Agile (Scrum, Kanban, SAFe), Waterfall, Hybrid — but the skill is *choosing and adapting* the framework to fit data programs, not dogmatically applying one. |

### Tier 3 — Valuable but Delegatable or Acquirable

| Skill | Type | Note |
|---|---|---|
| **Tooling Proficiency** | Hard | Jira/Jira Align, Confluence, ServiceNow SPM, Planview, Smartsheet, Azure DevOps (ADO). Important but learnable — don't screen out candidates over tool choices. |
| **Contracts & Procurement** | Hard | Time & Materials (T&M), fixed-price, managed services, SOW negotiation. Delegate to Project Management Office (PMO) or procurement; Director approves and escalates. |
| **Reporting & Metrics** | Hard | Executive dashboards, Objectives and Key Results (OKRs)/KPIs, velocity/throughput, DevOps Research and Assessment (DORA) metrics. The Director defines *what* to measure; the team builds the dashboard. |
| **Conflict Resolution** | Soft | Mediate between data engineering and business. Matters, but a Director with strong exec communication and influence rarely has unresolvable conflicts. |

### Experience Requirements

| Dimension | Target Profile |
|---|---|
| **Years in Delivery/PM** | 12–18+ years total; 5+ years at Senior Manager / Director level |
| **Portfolio Scale** | Managed 4–10+ concurrent programs; $10M–$50M+ combined annual budget |
| **Team Size** | Directed 30–100+ across direct reports and matrixed resources |
| **Domain** | Deep in data/analytics/AI; credible in conversations with data architects and ML engineers |
| **Industry** | Ideally cross-industry (financial services, healthcare, retail, tech) to bring pattern recognition |
| **Consulting vs. Product** | Both add value. Consulting builds client management and adaptability. Product builds depth and operational discipline. |
| **Certifications (valued, not required)** | Project Management Professional (PMP), Program Management Professional (PgMP), SAFe SPC / Release Train Engineer (RTE), Certified ScrumMaster (CSM), The Open Group Architecture Framework (TOGAF) Foundation, cloud certs (Amazon Web Services (AWS) / Azure / Google Cloud Platform (GCP)) for credibility with engineering teams |

### Resume Power Signals — What Hiring Managers Scan For

- "Delivered X programs totaling $YM across Z teams" — scale and concurrency.
- "Reduced delivery cycle time by X%" — proves continuous improvement, not just execution.
- "Managed data platform migration/build from discovery through hypercare" — end-to-end ownership.
- "Stood up delivery function / PMO for data organization" — builder, not just operator.
- "Coached N delivery leads to promotion" — leadership multiplier.
- "Navigated [specific hard thing]: org restructure during migration / sponsor change mid-program / regulatory deadline with shifting scope" — resilience under pressure.

---

## 5. Master Artifact Registry — By Delivery Stage

| Artifact | Responsibility Segment | Stage(s) | Owner |
|---|---|---|---|
| Portfolio Health Dashboard | Portfolio Execution Governance | All stages (continuous) | Director |
| Stage-Gate Checklist | Portfolio Execution Governance | Gate transitions (Discovery→Profiling→Architecture→Build→UAT→Hypercare) | Director / Program Lead |
| Program Roadmap | Portfolio Execution Governance | Initiation, updated through Execution | Director |
| Benefits Realization Tracker | Portfolio Execution Governance | Initiation (baseline), Hypercare & Post-delivery (actuals) | Director / Sponsor |
| Risk Register | Risk & Dependency Management | Initiation through Closure (continuous) | Program Lead, escalated to Director |
| Dependency Map / DSM | Risk & Dependency Management | Architecture, Build, Integration (updated at portfolio sync) | Director / Data Architect |
| Assumption Log | Risk & Dependency Management | Discovery, Profiling (created), monthly review through Closure | Program Lead |
| Issue Log + Escalation Matrix | Risk & Dependency Management | Build, UAT, Hypercare (continuous) | Program Lead, Director escalation |
| Communications Management Plan | Stakeholder & Client Management | Initiation (created), All stages (executed) | Director |
| Stakeholder Engagement Assessment Matrix | Stakeholder & Client Management | Initiation (baselined), quarterly refresh | Director |
| RACI Chart | Stakeholder & Client Management | Initiation (signed off), revisited at major phase transitions | Director / Program Lead |
| Change Request Log | Stakeholder & Client Management | Build, UAT (heaviest use), all stages (active) | Program Lead, Director approval |
| Lessons Learned Register | Stakeholder & Client Management | Sprint retros (input), Closure (formalized), cross-program quarterly | Director |
| Capacity Model / Resource Allocation Matrix | Team & Capacity Leadership | Initiation (planned), monthly refresh through Closure | Director |
| Team Charter | Team & Capacity Leadership | Kickoff (signed), revisited at retrospectives | Program Lead |
| Skills Inventory & Gap Analysis | Team & Capacity Leadership | Pre-initiation (hiring), quarterly refresh | Director |
| Utilization & Health Report | Team & Capacity Leadership | All stages (continuous, monthly cadence) | Director |
| Delivery Playbook | Process & Continuous Improvement | Organizational asset — maintained continuously, versioned quarterly | Director |
| Retrospective Action Register | Process & Continuous Improvement | Sprint boundaries (input), quarterly (systemic review) | Director / Delivery Community of Practice (CoP) |
| Process Metrics Dashboard | Process & Continuous Improvement | Build, UAT, Hypercare (continuous) | Director |
| Data Profiling Checklist | Process & Continuous Improvement | Discovery, Profiling (mandatory before Architecture gate) | Data Lead / Program Lead |
| Cutover Runbook | Process & Continuous Improvement | Pre-UAT (drafted), UAT (rehearsed), Go-live (executed) | Program Lead, Director sign-off |

---

*Document generated for Director of Delivery Management preparation — Data Project & Program Management scope. PMBOK 7th Edition alignment with select 6th Edition process tools. All techniques are verifiable against PMI published standards.*
