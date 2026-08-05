# Competency Profile — Principal AI Solution Architect

A self-contained competency profile and JD-scoring instrument for AI Solution Architect / Principal Architect roles that span Data, Cloud, AI/Agentic and Enterprise Architecture. Built as a precision view over `architecture-vocabulary-reference.md` (the recall layer — kept intact and unpruned; see Selection Rule).

---

## 1. Depth Ladder and Selection Rule

| Tier | Means | Test |
|---|---|---|
| **D — Design** | Can architect it, defend the trade-off, and own the decision record | Could you lead the design session? |
| **F — Fluent** | Can evaluate and govern it; can review someone else's design and find the risk | Could you find the flaw in a peer's proposal? |
| **A — Aware** | Can recognize and scope it, and route it to the right owner | Could you avoid saying something wrong in front of the specialist? |

**Selection rule (strict):** a competency enters this profile only if it is a **decision** or a **seam**, never a label. "Star schema" is a term; "grain of the core fact" is a decision — only the second is testable in an interview. This is why the profile holds ~70 rows against ~450 terms in the vocabulary file. The file optimizes recall (decode a JD's product names); this profile optimizes precision (build and defend an architecture). Neither is pruned to match the other.

---

## 2. Pillars and Weights

| Pillar | Weight | Verdict driving it |
|---|---|---|
| AI & Agentic | 35% | Differentiating competency in a Principal AI SA loop — Data/Cloud are screening, AI is separating |
| Seams & Cross-Cutting | 17% | The highest-value decisions sit between pillars, not inside one — a flat partition scored this at zero |
| Data Foundation | 18% | Correct core, two defects fixed (ontology added, governance-into-retrieval added) |
| Enterprise Architecture Method | 18% | Under-weighted at 20% shared; raised — this is the scoring machinery for everything else |
| Cloud Foundation | 12% | Over-weighted at 20%; three items were platform-engineering (L3), not architecture |

Weights sum to 100. Marked as a defensible allocation, not a derived optimum — see §7 Recalibration Rule.

---

## 3. Pillar Tables

### 3.1 AI & Agentic — 35%

| Competency | D/F/A | Evidence artifact | Interview stage |
|---|---|---|---|
| RAG vs fine-tune vs long-context | **D** | ADR comparing cost, quality, latency across the three paths on a real use case | Design exercise |
| Grounding source of truth (vector, knowledge graph, structured, hybrid) | **D** | Retrieval architecture diagram naming the grounding source and why | Design exercise |
| Agent topology (single, supervisor, hierarchical, peer) | **D** | Topology diagram with handoff and failure paths | Design exercise |
| Autonomy level and step/cost budget | **D** | Autonomy ladder with budget caps per agent tier | Design exercise |
| Tool permission scope and write-access policy | **D** | Tool registry with scoped, least-privilege permissions | Security panel |
| Human approval gate placement | **D** | Escalation design showing where humans sit in the loop | Design exercise · Exec panel |
| Guardrail placement and severity thresholds | **D** | Pre/in/post-call guardrail design with severity policy | Security panel |
| Model evaluation (groundedness, faithfulness, LLM-as-judge, regression gate) | **D** | Eval harness with named metrics and a CI regression gate | Deep-dive |
| Agent evaluation (trajectory, task success rate, tool-call accuracy, containment) | **D** | Separate eval suite scoring agent behaviour, not model output | Deep-dive |
| Fallback path definition | **D** | Documented behaviour when the model is unavailable or wrong | Design exercise |
| Irreversible-action policy and segregation of duties | **D** | Policy table: which actions require dual control | Security panel |
| Model selection and routing policy | **D** | Routing logic with cost/quality bands per task class | Deep-dive |
| Chunking, embedding, hybrid search, re-ranking | F | — | Deep-dive |
| Context assembly, truncation order, compaction | F | — | Deep-dive |
| Memory scope, persistence, retention | F | — | Deep-dive |
| Inference economics (cost per resolved task, TTFT, p99 under load) | F | Unit-economics model from real numbers | Exec panel |
| PII treatment before model invocation | F | — | Security panel |
| Prompt and model versioning with rollback | F | — | Deep-dive |
| Risk tier classification and oversight model | F | — | Governance discussion |
| Fine-tuning mechanics (LoRA/PEFT, distillation, quantization) | A | — | Deep-dive |
| Serving internals (KV cache, batching, speculative decoding) | A | — | Deep-dive |

### 3.2 Seams & Cross-Cutting — 17%

| Seam | D/F/A | The question it answers | Evidence artifact |
|---|---|---|---|
| Governance surviving into retrieval (RLS/CLS through embedding) | **D** | Does row/column security still hold once data is embedded? | Access-control test on the retrieval path |
| Agent identity and delegated authority | **D** | Whose authority does the agent act under? | Agent identity model doc |
| Action attribution | **D** | Who is accountable for what the agent did? | Audit trace linking action to identity |
| AI↔deterministic fallback boundary | **D** | What runs when AI can't or shouldn't? | Fallback architecture diagram |
| Trust-perimeter boundary | **D** | What data leaves, to whom, under what terms? | Data-flow diagram with egress points marked |
| Cloud↔AI: serving as a workload | F | Capacity, latency budget, failure domain | — |
| Enterprise policy plane | F | If each layer has its own policy engine, is there any enforceable policy? | — |
| Data gravity ↔ compute placement | F | What does residency force about topology? | — |

### 3.3 Enterprise Architecture Method — 18%

| Competency | D/F/A | Evidence artifact |
|---|---|---|
| Quality attribute scenarios (source, stimulus, artifact, environment, response, response measure) | **D** | One scenario per top-3 attribute, written in the six-part format |
| Sensitivity points and trade-off points | **D** | Annotated decision showing which attributes move and how |
| Risk, non-risk, risk theme | **D** | Risk register from a real or mock ATAM pass |
| Utility tree and business-driver weighting | **D** | Utility tree prioritizing 3–5 attributes against stated drivers |
| Architecture decision records | **D** | 3–5 real ADRs, each with context, decision, consequence |
| Four-block decomposition (any domain → quality attributes, seams, trade-offs, decisions) | **D** | Applied once, live, to a domain never seen before |
| Reference architecture and roadmap sequencing | F | One reference architecture with a phased roadmap |
| Architecture views and communication (C4 or equivalent) | F | One C4-style view set for a real system |
| Business capability mapping | F | — |

The four-block decomposition is the highest-leverage row in this profile — a general-purpose move that works on an unfamiliar domain, which is precisely what a design exercise tests.

### 3.4 Data Foundation — 18%

| Competency | D/F/A | Evidence artifact |
|---|---|---|
| Knowledge graph and ontology (property graph vs triple store, entity linking, GraphRAG) | **D** | Ontology sketch grounding a retrieval path |
| Relational vs graph vs hybrid representation | **D** | ADR on representation choice for a real entity model |
| Identity resolution rules and match thresholds (precision vs recall) | **D** | Match-rule table with stated thresholds and trade-off |
| Replicate or federate per source | **D** | Decision table per source system |
| Semantic layer placement (warehouse, BI tool, or API) | **D** | Placement diagram with rationale |
| Grain of the core fact | **D** | Fact table definition with grain stated explicitly |
| PII treatment (mask, tokenize, encrypt, synthesize) | **D** | Data classification and treatment matrix |
| Data-contract enforcement point | **D** | Contract spec with the enforcement boundary marked |
| Governance operating model (centralized vs federated) | F | — |
| Lineage, classification, RLS/CLS | F | — |
| Lakehouse, medallion, open table format | F | — |
| CDC, streaming, late-arriving data | F | — |
| Point-in-time correctness and look-ahead bias | F | — |
| MDM style, SCD type, partitioning keys, retention policy | A | — |

### 3.5 Cloud Foundation — 12%

| Competency | D/F/A | Evidence artifact |
|---|---|---|
| Synchronous vs asynchronous per interaction | **D** | Decision table per interaction type |
| Failure mode and fallback per dependency | **D** | Dependency map with failure behaviour per node |
| Event contract and versioning strategy | **D** | Event schema with a versioning policy |
| Cost and latency envelope (unit economics, p95/p99, latency tiering) | **D** | Cost model with real numbers from delivered work |
| SLO targets and error-budget policy | **D** | SLO table with error-budget policy stated |
| Event-driven patterns (saga, outbox, DLQ, idempotent consumer, backpressure) | F | — |
| Anti-corruption layer and integration boundaries | F | — |
| Tenant isolation model | F | — |
| Managed vs self-hosted per capability | F | — |
| State placement and session strategy | F | — |
| Zero trust, workload identity, scoped short-lived credentials | F | — |
| Graceful degradation, DR tiering, RTO/RPO | F | — |
| Containers, CI/CD, IAM policy mechanics, network topology | **A** | *Platform-engineering altitude, not architecture — deliberately capped* |

---

## 4. Out-of-Scope Filter — Reusable Forward Filter

A JD dominated by these is a **mandate mismatch, not a capability gap.** Naming them turns a rejection into a filter usable on the next JD, not just a verdict on this one.

- Platform operations — on-call, incident RCA, SRE runbooks, capacity/FinOps execution
- Engineering team management and hiring
- Hands-on pipeline or feature development
- Dedicated security architecture as a primary mandate (vs. the security-adjacent Seams rows above)
- Data engineering execution (pipeline build, DBT ownership)

**Diagnostic tell:** JD verbs *hire, run on-call, own incident RCA, hit uptime KPIs* → mandate mismatch. Verbs *design, sequence, govern, evaluate, advise* → this profile's lane.

---

## 5. JD Scoring View

Apply per JD. Score only rows the JD actually demands — do not force every row.

**Scoring notes:**
- A row demanded at D but held at F or below is a real gap — name the fastest close path (artifact, weeks).
- A row demanded at A generates no gap regardless of position — Aware-tier demand doesn't need Design-tier proof.
- If ≥2 Design-depth rows in a single pillar are Not Held, treat as a pillar-level flag, not five separate line items.
- Rows the JD is silent on are omitted from that JD's table, not scored as "Not held" — silence is not a gap.

---

### JD Scoring Log

#### JD-1 — AI Solution Architect, Cognida.ai (Hyderabad, India · Full-Time)

**JD character:** classical ML/DL/NLP/CV product-AI architecture with explicit hands-on Python development — not a GenAI/agentic-centric posting. No RAG, agents, tool-calling, guardrails, or LLM vocabulary appears anywhere in the source text. Cloud is named at familiarity level across all three hyperscalers, not mastery of one. No data-architecture depth (no semantic layer, identity resolution, ontology, governance language). No formal enterprise-method vocabulary (no ADR, ATAM, utility tree), though "communicate trade-offs to stakeholders" gestures at it informally.

| Pillar | Row demanded | Depth demanded | Held? | Gap |
|---|---|---|---|---|
| AI | Model evaluation (iterative refinement via "experimental data and feedback loops") | F | D | None — exceeds demand |
| AI | Fine-tuning mechanics (implied by "deep learning" as a primary pillar) | F | A | **Gap** — profile holds this at Aware only; JD implies Fluent. Close path: one fine-tuning build (LoRA/PEFT) on an open model, 1–2 weeks |
| AI | Model selection and routing (implied by "PoC through production" model lifecycle) | F | D | None |
| Enterprise | Communicating technical decisions and trade-offs to stakeholders | F | D | None |
| Enterprise | Translating business requirements into a solution blueprint | F | D | None |
| Enterprise | Four-block decomposition (implicit in "end-to-end, scalable AI architecture") | D | D | None |
| Cloud | Cloud platform familiarity — AWS, GCP, or Azure (explicitly "familiarity," not mastery) | A | F (exceeds A) | None |
| Cloud | MLOps for model deployment and maintenance | F | F | None |
| Data | *(JD is silent — no data-architecture rows demanded; omitted per scoring notes)* | — | — | — |
| Seams | *(JD is silent — no agentic/RAG content, so grounding, agent-identity, and retrieval-governance seams are not demanded)* | — | — | — |

**Out-of-scope filter check — flag, not a gap:** "hands-on development, integration and implementation" and "strong proficiency in Python" as an explicit, non-negotiable requirement both land inside §4's out-of-scope filter (hands-on pipeline/feature development). This is not scored as a capability gap — it is a **mandate signal**: the role blends architecture with IC-level build work, which is a narrower mandate than the Design-authority Principal lane this profile targets.

**Seniority check:** 14+ years is below this profile's implicit 15–19+ band, and the posting names no direct reports, no team-leadership language, and no design-authority or standards-ownership mandate ("leading development" refers to models/components, not people or architecture governance). Combined with the hands-on Python requirement, this reads as a **senior IC / hands-on architect role wearing an "AI Solution Architect" title**, not a Principal-altitude mandate.

**Verdict:** Rows actually demanded are held at or above the required depth — no capability gap of consequence. The disqualifying signal, if any, is **mandate altitude**, not skill: hands-on coding as a non-negotiable plus the absence of design-authority language suggests this is a builder role below the target seniority band. Score on capability alone would read strong; score on mandate fit reads as a step down. Recommend treating as **Get Ready / mandate-mismatch flag** rather than a clean Apply Now, and probing in a screening call whether "leading development" carries any design-authority or governance scope not visible in the posted text.

---

#### JD-2 — Senior AI Solution Architect (Specialist Solutions Architect – SSA), AWS India (Bengaluru/Mumbai/Gurugram)

**JD character:** a hyperscaler customer-facing technical-advisory role — GenAI/Agentic-centric, deep on AWS-native AI/ML services and hands-on production LLM deployment, near-silent on Data architecture and general cloud architecture patterns (no event-driven, sync/async, or data-modeling vocabulary appears). The mandate is field/pre-sales technical leadership (trusted advisor, roadmap influence, reference architectures, workshops) rather than internal enterprise Principal Architect ownership of a build.

| Pillar | Row demanded | Depth demanded | Held? | Gap |
|---|---|---|---|---|
| AI | RAG implementation (embeddings, vector stores, semantic search) — "practical RAG implementation" as a Basic Qualification | D | D | None |
| AI | Grounding source of truth / vector stores | D | D | None |
| AI | Agent topology / multi-agent orchestration (LangGraph, AutoGen, AWS AgentCore) | F | D | None — exceeds demand |
| AI | Guardrail placement (Guardrails for Bedrock) | F | D | None — **known-platform rule applied**: Guardrails for Bedrock is a documented sub-feature of AWS Bedrock, already held; credited as a match, not scored as a distinct AWS-tool gap |
| AI | Model evaluation / explainability (AWS Clarify, bias detection) | F | D (evaluation) / F (Clarify specifically) | None — **known-platform rule applied**: Clarify is a SageMaker sub-feature; SageMaker is held. Underlying competency (explainability, fairness metrics) matches §3 Evaluation vocabulary |
| AI | Model selection and routing (Bedrock/AgentCore/SageMaker ecosystem) | D | D | None |
| AI | **Serving internals — hands-on LLM production deployment on GPU/Neuron/TPU accelerators** | **D** | A | **Gap** — Basic Qualification, non-negotiable. Profile holds this row at Aware only. Severity: High (gating requirement, not preferred). Close path: one hands-on production-serving build on a named accelerator path (e.g., SageMaker + Neuron, or Bedrock provisioned throughput), 3–4 weeks; alternatively reframe existing Databricks Mosaic AI / Bedrock serving work explicitly in accelerator terms if it already touches this |
| AI | **Fine-tuning mechanics — PyTorch with JIT compilation and AOT tracing** | F (Preferred Qualification, stated as substitutable by general "professional software development" experience) | A | **Gap** — Low-Med severity since it is Preferred and JD offers a substitution path already met (software development experience). Close path: one PyTorch fine-tuning exercise noting JIT/AOT if pursued, 1–2 weeks; otherwise lean on the stated substitution clause |
| Enterprise | Reference architectures, whitepapers, code samples (Key Responsibility 4) | F | F | None |
| Enterprise | Architecture communication to diverse/executive audiences | F | D | None — exceeds demand |
| Enterprise | Four-block-style trade-off framing ("solution-strategy determination for simplifying or extending architectures") | D | D | None |
| Cloud | *(JD is silent on cloud architecture patterns — no event-driven, sync/async, SLO, or DR vocabulary; AWS services named are AI/ML tooling, not cloud-architecture decisions. Omitted per scoring notes, not scored as a gap.)* | — | — | — |
| Data | *(JD is silent — no data-modeling, identity-resolution, ontology, or governance vocabulary; "vector stores" is scored under AI/RAG per the profile's own placement, not Data. Omitted.)* | — | — | — |
| Seams | Trust-perimeter / responsible-AI boundary (Clarify, Guardrails, bias detection) | F | D | None — exceeds demand |
| Seams | Agent identity and delegated authority | *(JD is silent — AgentCore is named as an orchestration tool, but no identity/authority language appears)* | — | — |

**Out-of-scope filter check — flag, not a gap:** hands-on PyTorch coding and hands-on accelerator-level deployment both intersect §4's out-of-scope filter (hands-on pipeline/feature development). Unlike JD-1, this is not a disqualifying mandate signal here — AWS Specialist SA roles are structurally hybrid by design (deep technical credibility is the product being sold to the customer), so hands-on depth is load-bearing for the role's actual function, not a sign of a narrower IC mandate.

**Seniority check: PARITY, with a track-type caveat.** The stated bar (7+ years overall, 5+ years customer-facing, 5+ years ML/AI at scale) sits below the 19+ year profile on paper, but AWS Specialist SA is a recognized senior/principal-adjacent technical leadership track at the company, not a junior architect role — the gap is in the JD's stated minimums, not the role's actual altitude. The real signal to weigh is track type, not seniority: this is a **hyperscaler field/pre-sales advisory track** (customer-facing, roadmap-influencing, reference-architecture-producing) rather than an **enterprise-internal Principal Architect track** (design-authority, build-ownership, delivery governance). Both are legitimate Principal-adjacent lanes; they are not the same lane.

**Verdict:** Strong capability match on the AI pillar's core decisions (RAG, grounding, agent topology, model routing, evaluation) — the two real gaps are both narrow and accelerator/framework-specific (production serving on named AI hardware; PyTorch JIT/AOT), not architectural. The Basic-Qualification accelerator-deployment gap is the one item worth closing before applying, given it is non-negotiable rather than preferred. Enterprise-communication and trade-off competencies exceed demand. Cloud and Data pillars are structurally silent in this JD — this is not a weakness in the candidate or the profile, it reflects the role's actual scope (AI/ML specialist advisory, not a full-stack architecture build). Recommend **Get Ready (short close)**: one accelerator-serving artifact closes the sole High-severity gap; apply once built, or apply now while naming the close plan proactively given the role's advisory framing tolerates "in progress" more than a build-ownership role would.

---

## 6. Gap Closure Table — Self-Check

Every gap identified in the analysis that built this profile is mapped below to the row that closes it. Any row without a match is a defect in this document, not in the analysis.

| Gap | Closed by |
|---|---|
| Seams pillar unweighted in the original 4-way split | §2 — Seams pillar at 17% |
| Ontology/knowledge graph absent | §3.4 — Knowledge graph and ontology (D) |
| No governance-into-retrieval competency | §3.2 — Governance surviving into retrieval (D) |
| Cloud items at wrong altitude (Containers, CI/CD, IAM) | §3.5 — demoted to A, with rationale stated inline |
| IAM mislabeled (real competency is agent identity) | §3.2 — Agent identity and delegated authority (D), separate from IAM mechanics |
| Cost/latency envelope and inference economics absent | §3.5 Cost and latency envelope (D) · §3.1 Inference economics (F) |
| Agent evaluation conflated with model evaluation | §3.1 — two separate rows, both D |
| RAG vs fine-tune vs long-context unnamed as a decision | §3.1 — first row, D |
| Enterprise under-weighted; communication artifacts missing | §2 — raised to 18% · §3.3 Reference architecture, C4 views, capability mapping (F) |
| Uniform "Master" depth not credible | §1 — three-tier D/F/A ladder replaces it |

**Result: 10/10 closed.** No unmatched gap at time of writing.

---

## 7. Recalibration Rule — Self-Correcting

If a pillar is demanded at **Design depth in 2 or more of 3 scored JDs** while sitting below its current weight, raise it by 5 points and take those 5 points from the pillar demanded least across the same 3 JDs.

Record every adjustment here:

| Date | Trigger (3 JDs) | Pillar raised | Pillar reduced | New weights |
|---|---|---|---|---|
| *(log entries as JDs are scored)* | | | | |

---

## 8. Artifact Build Order and Study-Order Constraint

**Build order:** eval harness → agentic build → cloud class-analogue mapping.

**Study-order constraint (mandatory):** at least one Data-pillar Design-row artifact and one Cloud-pillar Design-row artifact must exist **before** a second AI-pillar artifact is built. AI's 35% weight and interview salience create a natural pull toward AI-only prep — this constraint forces Data/Cloud exposure to close in parallel rather than last.

Suggested minimum artifact set:
1. Eval harness (AI, D) — groundedness + agent trajectory metrics
2. **Gate: build one Data-pillar artifact** — e.g. identity-resolution match-rule table with stated precision/recall trade-off
3. **Gate: build one Cloud-pillar artifact** — e.g. cost-and-latency envelope with real numbers
4. Agentic build (AI, D) — multi-agent topology with tool permissions and a human approval gate
5. Cloud class-analogue mapping (Cloud, F) — service equivalence across AWS/Azure/GCP for the L2 layer

---

## 9. Deferred — Cross-Cutting, Next Revision

Excluded here because they cut across all five pillars above rather than belonging to one — adding them as a sixth bucket would repeat the exact partition error this profile corrects (§2, Seams rationale).

| Deferred area | Why it cuts across |
|---|---|
| Quality Engineering (prompt testing, response validation, eval-as-CI-gate, test automation architecture) | Appears inside Data (quality gates), Cloud (testability, chaos engineering), and AI (eval harnesses) — same discipline, three names |
| Accelerator and reusable-asset design | Applies to any pillar's output, not one |
| Stakeholder management, executive framing, pursuit and solutioning | Determines whether any architecture decision gets adopted — orthogonal to technical content |
| Delivery governance | Spans all four technical pillars |

Flag carried forward: the **deterministic test ↔ probabilistic AI evaluation** seam is where classical QE frameworks break against GenAI systems, and it is the likely centre of any AI-led QE conversation. It belongs in the Seams pillar, not a QE layer, when this revision lands.

---

## 10. Calibration

**Stated plainly:** the weights in §2 are a defensible allocation, not a derived optimum — three real scored JDs (§5) feeding the recalibration rule (§7) should overwrite them. The claim that this composition "covers most Principal AI Solution Architect interviews" is an **untested hypothesis** — no interview-stage model exists yet to verify it against, and §5's scoring log is the mechanism for testing it, not this document alone.
