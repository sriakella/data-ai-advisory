# Architecture Vocabulary Reference

**Usage rule:** Lists 1–3 are the **scoring vocabulary** — they may appear in rubric criteria. List 4 is **evidence vocabulary** — product names appear only when the JD supplies them, never in a scoring rule.

Appended to each section: **Quality attributes** (what the architecture is judged on) · **Seams** (boundaries where change, integration, and failure concentrate) · **Trade-offs** (the tensions with no free answer) · **Decisions** (the recurring ADR-worthy choices).

---

## 0. Cross-cutting — how to use a quality attribute

Applies to all four sections below. A bare ‑ility is a placeholder, not a requirement; the vocabulary in each section's **Quality attributes** subsection is only architecturally meaningful when expressed and analyzed through the machinery here.

**Quality attribute scenario format**
Source · Stimulus · Artifact · Environment · Response · Response measure.

> *Example:* On loss of a region (stimulus) during peak load (environment), the serving layer (artifact) returns degraded read-only responses (response) within 90 seconds (response measure).

**Prioritization**
Utility tree · business-driver weighting · importance (high/medium/low) × difficulty (high/medium/low) · scenario ranking.

**Analysis vocabulary**
Sensitivity point — a decision that materially moves one attribute · tradeoff point — a decision that moves two or more in opposite directions · risk — a decision whose consequence is unacceptable and unmitigated · non-risk — a decision confirmed safe under the stated scenarios · risk theme — a pattern across risks indicating a systemic driver.

---

## 1. Data Architecture — general terms

**Modeling & semantics**
Conceptual/logical/physical model · normalization (3NF) · dimensional model · star & snowflake schema · data vault · fact & dimension · grain · surrogate vs natural key · slowly changing dimensions · canonical data model · semantic layer · metrics layer · entity resolution · identity resolution · golden record · survivorship rules · master data management · reference data · hierarchy management · schema evolution · schema-on-read vs schema-on-write · polyglot persistence · knowledge graph · ontology · taxonomy · property graph vs RDF triple store · graph query language (Cypher, SPARQL, GQL) · entity linking · relationship inference · graph traversal depth

**Storage & platform patterns**
Data warehouse · data lake · lakehouse · data mart · operational data store · landing/staging zone · medallion (bronze/silver/gold) · open table format · columnar storage · partitioning · clustering · compaction · time travel · materialized view · hot/warm/cold tiering

**Movement & integration**
ETL · ELT · batch · micro-batch · streaming · change data capture · log-based replication · event sourcing · data federation · data virtualization · zero-copy access · push-down compute · reverse ETL · data contracts · idempotency · exactly-once vs at-least-once · backfill · replay · watermarking

**Governance & privacy**
Data governance · stewardship · column- and table-level lineage · catalog · business glossary · classification · PII/PHI tagging · masking · tokenization · encryption at rest/in transit · row- and column-level security · consent management · purpose limitation · retention policy · right to erasure · data residency · sovereignty · audit trail · RBAC/ABAC · least privilege

**Quality & reliability**
Completeness · accuracy · timeliness · validity · uniqueness · consistency · profiling · validation rules · freshness SLA · data downtime · data observability · reconciliation · deduplication · anomaly detection

**Serving & consumption**
Serving layer · OLTP/OLAP separation · HTAP · query federation · API/data access layer · feature store · embedding store · profile unification · segmentation · activation

**Operating model**
Data mesh · domain ownership · data product · federated governance · self-serve platform · DataOps · centre of excellence · showback/chargeback · data FinOps

### Quality attributes

Accuracy · completeness · timeliness/freshness · consistency · uniqueness · validity · availability · durability · scalability across volume, velocity and variety · query performance · interoperability · portability · evolvability under schema change · discoverability · self-service usability · auditability · lineage integrity · confidentiality · privacy · retention compliance · residency compliance · tenant isolation · recoverability · reproducibility · cost efficiency

### Seams

Source-system boundary · ingestion/landing boundary · raw→curated promotion boundary · storage–compute separation · batch–stream convergence point · operational↔analytical boundary · replication↔federation boundary · physical model↔semantic layer boundary · semantic layer↔serving/API boundary · producer↔consumer data contract · domain ownership boundary (mesh) · catalog↔pipeline metadata boundary · on-prem↔cloud boundary · tenant boundary · regulatory & residency boundary · archive/tiering boundary

### Trade-offs

Normalization vs query performance · replication vs federation · schema-on-write vs schema-on-read · freshness vs cost · consistency vs availability · latency vs completeness · centralized governance vs domain autonomy · one canonical model vs domain-local models · storage cost vs recompute cost · raw-grain retention vs aggregation · identity-match precision vs recall · access restrictiveness vs analyst velocity · vendor-managed convenience vs open-format portability · pipeline simplicity vs reprocessing flexibility

### Decisions

Table format selection · batch vs streaming per pipeline · replicate or federate per source · MDM style (registry, consolidation, coexistence, centralized) · identity resolution rules and match thresholds · grain of the core fact · SCD type per dimension · semantic layer placement (warehouse, BI tool, or API) · partitioning and clustering keys · retention and tiering policy · PII treatment (mask, tokenize, encrypt, synthesize) · data-contract enforcement point · governance operating model · sync direction and conflict resolution · quality gate placement (pre-load vs post-load) · relational vs graph vs hybrid representation for the entity model

---

## 2. Cloud Architecture — general terms

**Compute & runtime**
Serverless · function-as-a-service · container orchestration · managed runtime · horizontal & vertical autoscaling · cold start · warm pool · spot/preemptible capacity · right-sizing · bin packing

**Integration & messaging**
Event-driven architecture · publish/subscribe · message queue · event bus · event streaming · orchestration vs choreography · saga pattern · outbox pattern · dead-letter queue · idempotent consumer · backpressure · fan-out/fan-in · request-reply · webhook · API gateway · service mesh · circuit breaker · bulkhead · retry with exponential backoff

**Architecture styles**
Microservices · modular monolith · hexagonal · CQRS · event sourcing · strangler fig · sidecar · ambassador · anti-corruption layer · composable architecture · MACH · headless · API-first

**Topology & deployment**
Multi-tenant vs single-tenant · region & availability zone · active-active vs active-passive · multi-region · edge · CDN · hybrid · multi-cloud · cloud-native vs cloud-agnostic · landing zone · hub-and-spoke network · private link · network peering · egress cost

**Resilience & operations**
RTO/RPO · SLA/SLO/SLI · error budget · blast radius · graceful degradation · chaos engineering · blue-green · canary · feature flag · rollback · immutable infrastructure · infrastructure as code · GitOps · configuration drift · observability (metrics, logs, traces) · distributed tracing · correlation ID

**Security**
Zero trust · shared responsibility model · identity federation · SSO · OAuth2/OIDC · SAML · workload identity · secrets management · key management · envelope encryption · mTLS · network segmentation · WAF · defense in depth · compliance boundary · non-human/workload identity for autonomous agents · scoped and short-lived credentials · capability-based authorization

**Cost & performance**
Unit economics · cost per transaction · latency tiering · p50/p95/p99 · throughput · concurrency limits · quota & throttling · caching strategy · read replica · connection pooling · FinOps · reserved vs on-demand · tagging strategy

### Quality attributes

Availability · reliability · fault tolerance · resiliency · elasticity · scalability · latency · throughput · durability · consistency · security · privacy · regulatory compliance · observability · operability · maintainability · modifiability · deployability · testability · portability · interoperability · recoverability · tenant isolation · cost efficiency · sustainability

### Seams

API gateway / public contract boundary · service-to-service boundary · synchronous↔asynchronous boundary · event schema & registry boundary · control plane↔data plane boundary · identity/trust boundary · network perimeter (VPC, private link, segmentation) · tenant isolation boundary · region and availability-zone boundary · cloud↔on-prem boundary · cloud↔cloud boundary · shared-responsibility line at each managed service · build↔run boundary (IaC to runtime) · edge↔origin boundary · third-party/SaaS integration boundary

### Trade-offs

Consistency vs availability · latency vs durability · synchronous simplicity vs asynchronous resilience · orchestration control vs choreography autonomy · managed-service velocity vs lock-in · multi-cloud portability vs native optimization · microservice autonomy vs operational overhead · serverless elasticity vs cold-start latency · security restrictiveness vs developer velocity · redundancy cost vs RTO/RPO targets · cache freshness vs backend load · fine-grained scaling vs coordination cost · abstraction layers vs debuggability · standardization vs team autonomy

### Decisions

Region and AZ topology · active-active vs active-passive · synchronous vs asynchronous per interaction · event contract and versioning strategy · single- vs multi-tenant isolation model · identity and access model · network topology and egress path · managed vs self-hosted per capability · state placement and session strategy · deployment strategy (blue-green, canary, rolling) · SLO targets and error-budget policy · caching layer placement · failure mode and fallback per dependency · observability standard and retention · commitment model (reserved, on-demand, spot) · tagging and cost-allocation scheme

---

## 3. AI Architecture — general terms

**Classical ML**
Supervised · unsupervised · reinforcement learning · feature engineering · feature store · train/validation/test split · cross-validation · hyperparameter tuning · overfitting/underfitting · bias-variance tradeoff · class imbalance · ensemble methods · precision/recall/F1 · ROC-AUC · confusion matrix · probability calibration · propensity model · uplift model · champion/challenger · batch vs real-time inference

**MLOps**
Training pipeline · CI/CD for models · model registry · experiment tracking · model versioning · model lineage · shadow deployment · canary rollout · retraining trigger · train-serve skew · model drift · data drift · concept drift · explainability (SHAP, LIME) · fairness metrics · model card · reproducibility

**Generative AI**
Foundation model · LLM · small language model · multimodal · context window · tokenization · prompt engineering · system prompt · few-shot prompting · chain-of-thought · structured output · function/tool calling · grounding · retrieval-augmented generation · chunking strategy · embedding · vector index · similarity search (cosine, HNSW) · hybrid search · re-ranking · context engineering · prompt & semantic caching · fine-tuning · LoRA/PEFT · instruction tuning · distillation · quantization · RLHF · model routing · cascading · cost per token · time-to-first-token · token streaming · hallucination · groundedness · faithfulness · citation attribution · GraphRAG · ontology-grounded retrieval · schema-aware retrieval · structured + unstructured hybrid grounding

**Agentic AI**
Agent · tool use · planner/executor split · reflection · short-term & long-term memory · episodic & semantic memory · state management · orchestration topology (single-agent, supervisor, hierarchical, peer/swarm) · handoff · delegation · multi-turn conversation · human-in-the-loop · approval gate · autonomy level · action space · guardrails · sandboxed execution · deterministic fallback · failure recovery · loop detection · step & budget limits · agent trace · span · replay · agent observability

**Evaluation, safety & governance**
Eval harness · golden dataset · LLM-as-judge · rubric scoring · offline vs online evaluation · regression gate · red teaming · jailbreak resistance · prompt injection · indirect injection · data exfiltration · toxicity & safety classification · PII redaction · responsible AI · AI governance · model risk management · human oversight · kill switch · EU AI Act risk tiering · NIST AI RMF · ISO 42001

**Prompt testing & response validation**
Prompt regression testing · prompt versioning and diffing · deterministic assertion vs semantic assertion · schema/format conformance validation · structured-output validation · snapshot testing · negative and adversarial prompt suites · perturbation and paraphrase robustness testing · non-determinism budget and seed control · pass@k · self-consistency check · citation verification · refusal and over-refusal rate · response length and cost bounds

**Model & system evaluation frameworks**
Benchmark suite · eval dataset curation · stratified eval slices · human evaluation · inter-annotator agreement · pairwise/preference comparison · Elo and win-rate ranking · online A/B evaluation · statistical significance and sample sizing · eval drift · eval leakage · synthetic eval data generation · continuous evaluation in CI · evaluation-as-a-gate vs evaluation-as-telemetry

### Quality attributes

Accuracy · groundedness · faithfulness · relevance · coverage · robustness to input variation · consistency/determinism · time-to-first-token · end-to-end latency · throughput · cost per interaction · scalability · availability · graceful degradation · safety · fairness · explainability · transparency · privacy · injection resistance · controllability/steerability · autonomy-appropriateness · auditability · traceability · reproducibility · maintainability · adaptability to model change · observability · attributability · authorization containment · blast radius of autonomous action

### Seams

Context assembly boundary (what enters the prompt) · retrieval boundary (index↔generation) · model provider boundary (abstraction and routing) · tool-call boundary (agent↔enterprise system) · agent-to-agent handoff · human-in-the-loop approval gate · guardrail boundary (pre-call, in-flight, post-call) · memory boundary (session↔persistent) · trust-perimeter boundary (what data leaves) · training↔serving boundary · feature boundary (offline↔online) · evaluation boundary (offline harness↔production telemetry) · AI↔deterministic fallback boundary · prompt/version artifact boundary · agent identity boundary (whose authority the agent acts under) · delegated authority / on-behalf-of boundary · action attribution boundary (audit trail of who caused what)

### Trade-offs

Accuracy vs latency · quality vs cost per token · larger general model vs fine-tuned smaller model · RAG vs fine-tuning vs long-context · retrieval recall vs precision and context noise · chunk size vs semantic coherence · autonomy vs controllability · guardrail strictness vs task completion rate · determinism vs generative flexibility · memory richness vs privacy exposure · single-agent simplicity vs multi-agent capability · explainability vs model power · evaluation rigor vs delivery speed · human oversight vs throughput · response caching vs freshness · centralized model governance vs team velocity · provider abstraction vs native feature access

### Decisions

Build vs buy vs fine-tune · model selection and routing policy · RAG vs fine-tune vs hybrid · chunking and embedding strategy · vector store selection and placement · context assembly and truncation order · agent topology (single, supervisor, hierarchical, peer) · autonomy level and step/cost budget · tool permission scope and write-access policy · human approval gate placement · memory scope, persistence and retention · guardrail placement and severity thresholds · evaluation metrics and regression-gate thresholds · fallback path definition · PII treatment before model invocation · trace capture and retention · retraining/index-refresh trigger · prompt and model versioning with rollback strategy · risk tier classification and oversight model · grounding source of truth (vector index, knowledge graph, structured query, or hybrid) · agent identity model (service identity vs impersonation vs delegated user authority) · irreversible-action policy and segregation of duties · attribution and audit granularity per agent action · evaluation framework selection and whether evals gate release or only observe · assertion style per test class (deterministic, schema, semantic, judge-scored) · golden-dataset ownership and refresh cadence · acceptable non-determinism budget per use case

---

## 4. Platforms & Tools — evidence vocabulary

| Vendor | Platform / tool | Instantiates (from lists 1–3) |
|---|---|---|
| **Salesforce** | Data Cloud | identity resolution · semantic layer · zero-copy federation · profile unification · activation |
| | Agentforce | agent orchestration · tool use · guardrails · human-in-the-loop |
| | Health Cloud | industry data model · PHI handling · consent management |
| | Financial Services Cloud | industry data model · relationship hierarchy · regulatory reporting |
| | Marketing Cloud / MC Personalization | segmentation · activation · real-time decisioning |
| | Einstein / Prediction Builder | propensity model · explainability |
| | MuleSoft | API gateway · integration layer · anti-corruption layer |
| | Tableau / CRM Analytics | serving & consumption layer |
| **AWS** | AWS Cloud | region/AZ topology · landing zone · IAM |
| | Bedrock / AgentCore | foundation model access · model routing · agent runtime |
| | SageMaker | training pipeline · model registry · feature store |
| | Lambda · EventBridge · Step Functions · SQS/SNS | serverless · event bus · orchestration · queue |
| | S3 · Glue · Redshift · Athena · Kinesis | object store · ETL · warehouse · federation · streaming |
| | OpenSearch / Kendra | vector index · hybrid search |
| **Microsoft Azure** | Azure Cloud | region topology · landing zone · Entra identity |
| | Azure AI Foundry | foundation model access · eval harness · agent orchestration |
| | Azure OpenAI Service | LLM serving · content filtering |
| | Azure ML | training pipeline · model registry · responsible AI dashboard |
| | Microsoft Fabric / OneLake | lakehouse · open table format · shortcuts (zero-copy) |
| | Synapse · Data Factory · Event Hubs · Service Bus | warehouse · ELT · streaming · messaging |
| | AI Search · Cosmos DB | vector index · hybrid search · polyglot persistence |
| | Purview | catalog · lineage · classification |
| | Semantic Kernel / Copilot Studio | agent framework · tool calling |
| **Google Cloud** | GCP | region topology · landing zone · workload identity |
| | Vertex AI / Agent Builder | model serving · training pipeline · agent orchestration |
| | Gemini models | foundation model · multimodal · long context |
| | BigQuery / BigLake | warehouse · federation · open table format |
| | Dataflow · Pub/Sub · Dataproc · Composer | streaming · pub/sub · batch · orchestration |
| | Vertex Vector Search | vector index · similarity search |
| | Dataplex | catalog · governance · quality |
| **Databricks** | Lakehouse Platform | lakehouse · medallion · Delta open table format |
| | Unity Catalog | catalog · lineage · row/column security |
| | Mosaic AI | RAG · fine-tuning · model serving · eval |
| | Delta Live Tables · MLflow · Feature Store | pipelines · experiment tracking · feature store |
| **Snowflake** | Data Cloud | warehouse · zero-copy clone · secure data sharing |
| | Cortex AI / Cortex Analyst | LLM functions · semantic model |
| | Snowpark · Iceberg Tables · Streams & Tasks | push-down compute · open table format · CDC |
| **Cross-vendor** | LangGraph · LangChain · AutoGen · CrewAI · LlamaIndex | agent orchestration · planner/executor · memory |
| | LangSmith · Langfuse · Arize · W&B | agent trace · eval harness · drift monitoring |
| | DeepEval · Ragas · Promptfoo · Giskard · TruLens · OpenAI Evals | prompt regression testing · response validation · LLM-as-judge · eval-as-CI-gate |
| | Selenium · Playwright · Cypress · Appium · REST Assured · JMeter · k6 · Pact | functional, API, performance and contract test automation |
| | TestRail · Xray · Zephyr · Allure | test management · traceability · coverage reporting |
| | Pinecone · Weaviate · Milvus · pgvector | vector index · similarity search |
| | dbt · Airflow · Fivetran · Kafka · Debezium | transformation · orchestration · ingestion · streaming · CDC |
| | Collibra · Alation · OneTrust · Immuta | catalog · glossary · consent · policy enforcement |
| | Terraform · Kubernetes · Docker | IaC · container orchestration |

**Read the table left-to-right when a JD names a product** — it tells you which neutral capability the product is actually testing. **Read it right-to-left when writing a resume line or interview answer** — lead with the pattern, cite the product as the receipt. That direction discipline is what keeps a vendor name from becoming a scoring rule again.

### Quality attributes — how platforms themselves get judged

Capability fit to the required pattern · maturity and GA status of the specific feature · ecosystem and connector breadth · exit cost and data portability · open-format support · licensing and consumption cost model · scaling behaviour under real load · regional and residency availability · certification and compliance coverage · release cadence and breaking-change risk · talent availability in the market · vendor roadmap alignment · support model and escalation path

### Seams — where platform choices become architectural commitments

Proprietary format↔open format boundary · platform-native compute↔external compute boundary · vendor identity model↔enterprise IdP boundary · vendor catalog↔enterprise catalog boundary · vendor agent runtime↔enterprise tool/API boundary · vendor model garden↔external model provider boundary · SaaS application data model↔analytical data model boundary · per-vendor governance policy↔enterprise policy boundary · licensing/consumption metering boundary

### Trade-offs

Native integration depth vs portability · single-vendor coherence vs best-of-breed capability · managed convenience vs configurability · consumption pricing elasticity vs cost predictability · fast time-to-value vs long-term exit cost · vendor-embedded AI vs independently governed AI · proprietary performance optimization vs open-format neutrality · one governance plane vs specialized tooling per domain

### Decisions

Primary data platform selection · primary AI/model platform selection · where the system of record sits versus the system of insight · open table format commitment · abstraction layer over model providers (yes/no and how thick) · which governance plane is authoritative when two vendors both offer one · build on vendor agent runtime vs framework-neutral orchestration · which integrations are native and which go through a neutral layer · exit strategy and the data-extraction path defined at selection time, not at renewal

---

## 5. Quality Engineering & Solutioning — general terms

Scoring vocabulary, same status as sections 1–3. Added because QE-as-architecture and pursuit/solutioning are orthogonal to the data/cloud/AI triad — a role can demand either without demanding any of the other three, and forcing them into §3 would misclassify both.

**Test strategy & discipline**
Test pyramid · unit, integration, system, end-to-end · contract testing · consumer-driven contracts · exploratory testing · risk-based test prioritization · shift-left · shift-right / testing in production · smoke, sanity, regression suites · non-functional testing · performance, load, soak, stress, spike testing · resilience and failure-injection testing · accessibility testing · security testing (SAST, DAST, penetration) · data-quality testing · UAT and business acceptance

**Automation architecture**
Test automation framework · page object / screenplay pattern · keyword- and data-driven testing · behaviour-driven development · test data management · synthetic test data generation · environment and service virtualization · stub, mock, fake, contract double · parallel and distributed execution · flakiness and quarantine policy · idempotent and self-cleaning tests · CI/CD quality gate · code and requirement coverage · traceability matrix · defect leakage and escape rate · mean time to detect

**AI-led quality engineering**
AI-assisted test case generation from requirements · test scenario derivation from user stories or specs · coverage-gap analysis · self-healing locators and test maintenance · intelligent test selection and impact analysis · risk prediction and defect-hotspot modelling · automated root-cause clustering of failures · synthetic data generation for test fixtures · AI-generated test oracles and assertions · human review gate on generated tests

**Accelerator & reusable-asset design**
Accelerator · reusable asset · reference implementation · blueprint · starter kit · harness · jumpstart · asset harvesting · productization of delivery IP · configurability vs hard-coding · asset versioning and support model · onboarding time-to-first-value · reuse rate and asset adoption metric · maintenance ownership after handover

**Pursuit, solutioning & stakeholder management**
RFP / RFI / RFQ response · solution shaping · win theme · value proposition and business case · orals and defence · proof of concept vs proof of value · estimation model (bottom-up, analogous, parametric) · effort and complexity sizing · pricing model (T&M, fixed-price, outcome-based, managed service) · delivery model (onshore, offshore, hybrid, pod, factory) · ramp plan and staffing curve · statement of work and scope boundary · assumptions, dependencies and exclusions · transition and knowledge-transfer plan · TCO and ROI narrative · stakeholder map and influence analysis · RACI · executive summary discipline · objection handling · commercial-technical trade-off

### Quality attributes

Defect detection effectiveness · escape rate · coverage (requirement, code, risk) · test reliability / flakiness rate · execution time and feedback latency · maintainability of the test suite · determinism · environment parity with production · traceability from requirement to test to defect · reusability of the asset across engagements · time-to-first-value for an accelerator · estimation accuracy · scope stability · stakeholder confidence and decision velocity

### Seams

Requirement↔test-case boundary (traceability) · test↔environment boundary (parity and data) · automation suite↔CI pipeline gate · functional↔non-functional test boundary · deterministic test↔probabilistic AI evaluation boundary · generated-test↔human-review boundary · accelerator↔client-specific customization boundary · asset ownership handover boundary (delivery team↔support team) · solution↔commercial boundary (what was scoped vs what was priced) · pursuit↔delivery handoff boundary · client stakeholder↔delivery organization boundary

### Trade-offs

Coverage breadth vs suite execution time · automation investment vs feature velocity · deterministic assertion reliability vs semantic assertion realism for AI outputs · generated-test volume vs review burden and trust · accelerator configurability vs simplicity and support cost · reuse across clients vs fit to one client · early quality gates vs delivery throughput · testing in production insight vs blast radius · aggressive estimate to win vs deliverable margin · scope generosity vs delivery risk · single-vendor accelerator lock-in vs client portability · depth of discovery vs pursuit cost

### Decisions

Test pyramid shape and where automation stops · which quality gates block a release vs report only · deterministic vs LLM-judged assertion per test class · build vs adopt the automation framework · test data strategy (masked production, synthetic, generated) · environment strategy and virtualization boundary · flakiness tolerance and quarantine policy · which generated tests require human sign-off · what becomes an accelerator vs stays engagement-specific · accelerator support and versioning model · pricing and delivery model per pursuit · estimation basis and contingency · which assumptions are stated in the SOW vs absorbed · who owns the architecture narrative in orals

---
---

# Appendix — Steelman & Gap Register

## Steelman of the lists as they stand

Before listing gaps, the strongest defense of the current state, because two of the obvious "gaps" fail against it:

**A vocabulary is a discriminating instrument, not an encyclopedia.** Its function is to let a JD be decomposed quickly and consistently. Every term added dilutes recall and increases the chance that scoring drifts toward whichever term the reader happened to notice. A list you cannot hold in working memory does not get used — it gets skimmed. *Therefore: "term X is missing" is only a valid criticism if X changes a scoring or design decision, not merely if X exists.*

**Apparent duplication across lists is correct, not sloppy.** Feature store, observability, lineage, and drift each appear in more than one list. That is not ambiguity — those terms sit precisely on the seams between data, cloud and AI, and a seam term belongs to both sides. Forcing single ownership would hide the seam.

**Omitting fast-moving vocabulary is defensible.** A term list stable enough to score against should not chase quarterly naming. Protocol and product names churn; patterns do not.

**What survives that defense:** gaps that are *structural* (a whole reasoning move is unavailable), not *lexical* (a synonym is absent). The register below is filtered to those. Gaps that failed the filter are recorded at the end so the filtering itself is auditable.

---

## Gap Register — structural gaps that survive the steelman

### G1 — No graph, ontology, or knowledge-graph vocabulary anywhere
**Severity: High · Sections affected: 1 and 3**

Neither the data list nor the AI list contains: knowledge graph · ontology · taxonomy · triple store · RDF/OWL · property graph · Cypher/SPARQL/GQL · graph traversal · entity linking · GraphRAG · ontology-grounded retrieval · semantic reasoning.

**Why it is structural, not lexical:** this omission makes an entire class of architecture unscoreable. A JD asking for ontology-driven grounding, semantic reasoning over a knowledge graph, or GraphRAG has no term in the vocabulary to map to — so it either scores as an unnamed gap or gets silently mapped to "semantic layer," which is a different construct entirely (a metrics abstraction, not a relationship model). This is also the single most common bridge between enterprise data architecture and AI grounding, i.e. exactly the seam an AI Solution Architect is hired to own.

**Append to §1 Modeling & semantics:** knowledge graph · ontology · taxonomy · property graph vs RDF triple store · graph query language · entity linking · relationship inference · graph traversal depth
**Append to §3 Generative AI:** GraphRAG · ontology-grounded retrieval · structured + unstructured hybrid grounding · schema-aware retrieval
**Append to §1 Decisions:** relational vs graph vs hybrid representation for the entity model
**Append to §3 Decisions:** grounding source of truth — vector index, knowledge graph, structured query, or hybrid

---

### G2 — No agent identity, delegated authority, or authorization seam
**Severity: High · Sections affected: 2 and 3**

Missing entirely: agent identity · non-human identity · delegated authority · on-behalf-of authorization · scoped token · capability-based access · service account sprawl · action attribution · consent-to-act · segregation of duties for autonomous actions.

**Why it is structural:** the AI list has a "tool-call boundary" seam and a "tool permission scope" decision, but neither answers *whose authority the agent acts under*. In enterprise deployment this is the most frequent blocker between a working agent prototype and production approval — security and audit reject on identity and attribution long before they reject on model quality. An AI Solution Architect who cannot articulate this seam is exposed on the security-review question that is now standard in final-round panels. The vocabulary currently gives no vocabulary to answer it with.

**Append to §3 Seams:** agent identity boundary (whose authority the agent acts under) · delegated authority / on-behalf-of boundary · action attribution boundary (audit trail of who caused what)
**Append to §2 Security:** non-human/workload identity for autonomous agents · scoped and short-lived credentials · capability-based authorization
**Append to §3 Decisions:** agent identity model (service identity vs impersonation vs delegated user authority) · irreversible-action policy and segregation of duties · attribution and audit granularity per agent action
**Append to §3 Quality attributes:** attributability · authorization containment · blast radius of autonomous action

---

### G3 — Quality attributes are listed but not operationalized
**Severity: High · Sections affected: all four**

Each section lists quality attributes as a flat set of ‑ilities. There is no mechanism to make them decision-useful: no **quality attribute scenario** format, no **utility tree** for prioritization, no **sensitivity point** or **tradeoff point** vocabulary, no **risk / non-risk** distinction.

**Why it is structural:** a flat list of ‑ilities cannot be argued with, prioritized, or tested. "Scalable and secure and observable" is true of every system and discriminates nothing. ATAM's contribution is precisely the machinery that turns those words into falsifiable statements — and that machinery is absent. This is the gap that most directly costs points in an interview: candidates who say "availability, scalability, security" sound identical; candidates who say "availability scenario: on loss of a region during peak, the system serves degraded read-only within 90 seconds — that's the sensitivity point, and it trades against write consistency" do not.

**Append as a new cross-cutting subsection (applies to all four sections):**

> **Quality attribute scenario format** — Source · Stimulus · Artifact · Environment · Response · Response measure. A quality attribute is only architecturally meaningful when expressed in this form; a bare ‑ility is a placeholder, not a requirement.
>
> **Prioritization** — utility tree · business-driver weighting · high/medium/low importance × high/medium/low difficulty.
>
> **Analysis vocabulary** — sensitivity point (a decision that materially moves one attribute) · tradeoff point (a decision that moves two or more in opposite directions) · risk (a decision whose consequence is unacceptable and unmitigated) · non-risk (a decision confirmed safe under the stated scenarios) · risk theme (a pattern across risks that indicates a systemic driver).

---

### G4 — No inference serving, capacity, or unit-economics vocabulary
**Severity: Med-High · Section affected: 3**

Missing: inference server · continuous/dynamic batching · KV cache · prefill vs decode · speculative decoding · accelerator capacity and quota · model cold-load · concurrency per replica · tail latency (p99) under load · autoscaling for inference · provisioned vs on-demand throughput · cost per resolved task.

**Why it is structural:** the AI list has "cost per token" and "time-to-first-token," which are metrics, but nothing describing the *serving architecture* that produces them. This is the layer where AI architecture stops being conceptual and starts being an operable system, and it is where the difference between an AI enthusiast and an AI architect is measured. A JD asking for "production LLM serving at scale" currently maps to nothing.

**Append to §3 Generative AI:** inference server · continuous batching · KV cache · prefill vs decode phase · speculative decoding · accelerator capacity and quota · model cold-load time · provisioned vs on-demand throughput
**Append to §3 Quality attributes:** tail-latency stability under load · throughput per accelerator · capacity predictability · cost per resolved task
**Append to §3 Trade-offs:** batching throughput vs per-request latency · provisioned capacity cost vs burst headroom · hosted inference vs self-hosted control
**Append to §3 Decisions:** inference hosting location and residency · capacity model (provisioned, on-demand, hybrid) · degradation policy when capacity is exhausted

---

### G5 — No agent-specific evaluation vocabulary
**Severity: Med-High · Section affected: 3**

The evaluation subsection covers model-output evaluation (groundedness, faithfulness, LLM-as-judge). It contains nothing for evaluating *agents*, which are evaluated on trajectories and outcomes, not on single responses.

Missing: trajectory evaluation · task success rate · tool-call accuracy · step efficiency · containment/deflection rate · escalation precision · recovery rate after failure · cost per successful task · turn count to resolution · regression across model versions.

**Why it is structural:** an agentic architecture whose evaluation vocabulary is single-turn output quality is unmeasurable in the dimension that matters. This is also the highest-frequency second-round interview question in agentic roles — "how do you know it's working?" — and the current list can only answer it for a chatbot.

**Append to §3 Evaluation, safety & governance:** trajectory evaluation · task success rate · tool-call accuracy · step efficiency · containment and deflection rate · escalation precision · failure recovery rate · turn-count to resolution · cross-version regression suite
**Append to §3 Quality attributes:** task success rate · containment rate · escalation quality · behavioural stability across model versions

---

### G6 — No temporal correctness or point-in-time vocabulary
**Severity: Med · Sections affected: 1 and 3**

Missing: bitemporal modeling · valid time vs transaction time · as-of query · point-in-time correctness · late-arriving data · out-of-order events · effective dating · restatement · look-ahead bias.

**Why it is structural:** point-in-time correctness is the defining constraint in financial services, insurance, healthcare claims, and regulatory reporting — and it is also the single most common cause of silent failure in ML feature pipelines (train-serve leakage from future data). "Slowly changing dimensions" and "time travel" are present but describe storage mechanics, not the correctness property. A regulated-industry JD probing this has no matching term.

**Append to §1 Modeling & semantics:** bitemporal modeling · valid time vs transaction time · as-of query · effective dating · restatement handling
**Append to §1 Movement & integration:** late-arriving data · out-of-order event handling
**Append to §1 Quality attributes:** point-in-time correctness · temporal consistency
**Append to §3 MLOps:** look-ahead bias · point-in-time-correct feature retrieval

---

### G7 — No privacy-enhancing technology vocabulary
**Severity: Med · Sections affected: 1, 2 and 3**

Masking, tokenization and encryption are present. Missing: differential privacy · k-anonymity · l-diversity · pseudonymization vs anonymization (a legal distinction, not a synonym) · synthetic data generation · data clean room · federated learning · confidential computing / trusted execution environment · homomorphic encryption · BYOK / CMEK / HYOK key custody.

**Why it is structural:** these are the mechanisms by which AI systems get approved in regulated environments. Without them the only available answers to "how do you use this data safely" are mask/tokenize/encrypt, which are insufficient for model training, cross-party collaboration, and multi-tenant AI. The pseudonymization-vs-anonymization distinction in particular is legally load-bearing under GDPR and is frequently tested.

**Append to §1 Governance & privacy:** pseudonymization vs anonymization · differential privacy · k-anonymity · synthetic data generation · data clean room · federated learning
**Append to §2 Security:** confidential computing / trusted execution environment · BYOK · CMEK · HYOK key custody · homomorphic encryption
**Append to §3 Decisions:** privacy technique selection for training and inference data · cross-party data collaboration mechanism

---

### G8 — Cloud section lacks DR tiering and delivery-performance measures
**Severity: Med · Section affected: 2**

RTO/RPO are present as targets, but the DR strategy vocabulary that satisfies them is missing: backup-and-restore · pilot light · warm standby · hot standby / multi-site active-active · failover and failback runbook · recovery testing cadence. Also missing: the DORA delivery measures — deployment frequency · lead time for change · change failure rate · mean time to restore — which are the standard evidence for the "deployability" and "operability" attributes already listed.

**Why it is structural:** the section can state a target but cannot name the architecture that meets it, and can name "maintainability" and "deployability" as attributes without any measure attached — which is G3's problem recurring in concrete form.

**Append to §2 Resilience & operations:** backup-and-restore · pilot light · warm standby · hot standby · failover and failback runbook · recovery testing cadence · deployment frequency · lead time for change · change failure rate · mean time to restore
**Append to §2 Decisions:** DR tier per workload class · recovery test frequency and ownership

---

### G9 — Platform list omits model providers as a category, and several major enterprise vendors
**Severity: Med · Section affected: 4**

The table maps hyperscalers, data platforms and cross-vendor tools, but has **no row class for model providers themselves** — the entity actually being selected in "model selection and routing policy," which is a §3 decision. Anthropic, OpenAI, Google, Meta, Mistral, Cohere, and open-weight families are absent, as is the open-weight vs proprietary distinction.

Also absent as vendors: IBM (watsonx) · Oracle · SAP · ServiceNow · Adobe (Experience Platform / RT-CDP) · Informatica · Palantir · Teradata · Cloudera · Segment · Tealium · Boomi · Workato.

Absent as **tool categories**: identity providers (Okta, Entra ID, Ping) · API management (Apigee, Kong, APIM) · workflow orchestration (Temporal, Prefect, Dagster) · semantic layers (Cube, AtScale, dbt Semantic Layer) · reverse ETL (Hightouch, Census) · data quality (Great Expectations, Soda, Monte Carlo) · open metadata (DataHub, OpenLineage, OpenTelemetry) · guardrail tooling (NeMo Guardrails, Guardrails AI) · eval tooling (Ragas, DeepEval) · graph databases (Neo4j, TigerGraph) · agent interoperability protocol implementations.

Within listed vendors, notable per-vendor omissions where the missing item is the *governance or semantic* component — i.e. the architecturally significant one: Salesforce **Einstein Trust Layer** (masking, zero-retention, audit — the component a Salesforce AI architecture question is actually about), Salesforce Shield, Platform Events, Salesforce Connect · AWS **Bedrock Guardrails**, Bedrock Knowledge Bases, Lake Formation, Neptune · Azure **AI Content Safety**, Key Vault, API Management · Google **Looker** (the semantic layer, notably missing while BigQuery is present), Apigee, Model Armor · Databricks **Delta Sharing**, Genie · Snowflake **Horizon**, Data Clean Rooms, Dynamic Tables.

**Why it is structural:** the table's stated purpose is left-to-right decoding of JD product names into neutral capabilities. A missing category means a JD term decodes to nothing. Model providers are the most consequential omission because model selection is an explicit §3 decision with no corresponding evidence row.

**Append a `Model providers` vendor class** with the open-weight vs proprietary axis, hosting location, data-usage terms, and deprecation cadence as the mapped attributes. **Append the missing tool categories** as cross-vendor rows.

---

### G10 — No cross-list seam map
**Severity: Med · Sections affected: all**

The three neutral lists are presented in parallel with no explicit statement of where they meet. The most consequential architecture decisions for an AI Solution Architect sit *between* the lists, not inside any one of them.

**Why it is structural:** the whole premise of the AI Solution Architect rubric is that AI cannot be evaluated apart from its data and cloud foundations. The vocabulary encodes the three foundations but not the joints — so the very thing the role is defined by is the thing the vocabulary is silent on.

**Append as a closing cross-cutting subsection:**

> **Data↔AI seam** — governed data becoming retrievable context: what is indexable, at what freshness, under whose access policy, with lineage preserved through embedding. Row/column security must survive the transition into a vector index or it is not enforced.
>
> **Cloud↔AI seam** — model serving as a workload: capacity, latency budget, failure domain, egress of prompt and response data across trust boundaries.
>
> **Data↔Cloud seam** — data gravity, residency constraint on compute placement, and cost of movement determining topology.
>
> **Three-way seam** — the enterprise policy plane: one authoritative source for identity, classification and audit across data stores, cloud workloads and AI agents. When each of the three has its own policy engine, the architecture has no enforceable policy at all.

---

## Gaps considered and rejected — the audit trail for the filter

Recorded so the exclusion is reviewable rather than assumed.

| Candidate gap | Why rejected |
|---|---|
| Specific agent protocol names (MCP, A2A, tool registries) | Fails the churn test as *names*; the underlying need is already captured by G2 (identity/authority) and the existing "tool-call boundary" seam. Add the protocol names to §4 as evidence vocabulary, not to §1–3 as scoring vocabulary. |
| Duplicate terms across lists (feature store, observability, drift, lineage) | Not a defect. These sit on seams and legitimately belong to both sides. Forcing single ownership would obscure G10. |
| Missing ACID / isolation levels / MVCC | Transactional database internals. Real, but below the altitude at which a solution architect is scored; belongs to an engineering vocabulary. |
| Missing performance internals (shuffle, skew, small-file problem, cardinality) | Same reason — platform-engineering depth, not architecture decision content. Would inflate the list without changing any decision. |
| Missing SR 11-7 and model validation / challenger governance | Genuinely useful, but narrow to banking model risk. Merge into the existing "model risk management" term rather than expanding the list; note it as a domain overlay, not a core term. |
| Missing sustainability/carbon detail beyond the one attribute | Rarely a scored requirement outside EU public sector. One attribute is proportionate coverage. |
| Missing accessibility, localization, i18n | Real quality attributes, but they attach to application architecture rather than data/cloud/AI architecture as scoped here. |

---

## Pareto — if only three gaps are closed

| Rank | Gap | Why it carries the most |
|---|---|---|
| 1 | **G3 — operationalize quality attributes** (scenarios, utility tree, sensitivity/tradeoff points, risks) | Upgrades all four sections at once. Converts flat ‑ility lists into falsifiable architecture statements. This is the difference between sounding like an architect and being scoreable as one. |
| 2 | **G2 — agent identity and delegated authority** | The most common production blocker in agentic architecture and the most predictable security-panel question. Currently unanswerable from the vocabulary. |
| 3 | **G1 — graph and ontology vocabulary** | Closes the single largest bridge between enterprise data architecture and AI grounding — precisely the seam the AI Solution Architect role is defined by. |

G4–G10 are additive and can be absorbed incrementally without restructuring anything.
