# Architecture Ontology Reference

**Why this exists.** You could recall vocabulary but couldn't name an observable action in a Response. Diagnosis: R1 trains four facets per layer — components (nouns), quality attributes, seams, trade-offs, decisions — but omits the two facets S-S-A-E-R-M actually draws on:

- **Behavioral repertoire (verbs)** — the finite set of observable actions a layer can take. This is what a **Response** is built from.
- **Failure modes** — how the layer degrades or breaks. This is what a **Stimulus** targets and what the **Environment/Response Measure** is written against.

Master these two per layer and the framework becomes writable. Everything else here is R1-grounded and included so each layer is self-contained.

## The knowable surface of any layer (the ontology template)

Whatever tier a layer sits in, its full ontology is:

| Facet | What it answers | Feeds which S-S-A-E-R-M field |
|---|---|---|
| **Purpose** | Why this layer is first-order / exists | Frames Artifact |
| **Elements** | The tech-agnostic components that instantiate it | Names the Artifact precisely |
| **Behavioral repertoire (verbs)** | The observable actions it can take | **Response** |
| **Failure modes** | How it degrades or breaks | **Stimulus**, and the thing Response Measure falsifies |
| **Quality attributes** | What it's judged on (the forces that live here) | Force A / Force B |
| **Seam(s)** | The boundary it forms with neighbours | Where the Decision lives |
| **Decisions** | The recurring ADR-worthy choices | The architectural choice |
| **Trade-offs** | The tensions with no free answer | The opposing force |

**Framing correction to your proposal:** you had Domain = (components, concepts, elements, purpose); Decision = (traits, characteristics, intent); Operations = (types, levels, rules, conditions). That's directionally right but it buries the two facets you were missing. In this reference, *every* tier carries a behavioral repertoire and a failure-mode set — those are not optional extras, they are the facets that made your Response field impossible to write.

- **Domain** (first-order, independent — removing it breaks the architecture): lead with Purpose + Elements + **Verbs** + **Failure modes**.
- **Decision category** (how domains interact/scale/govern — removing it degrades quality): lead with Intent + Traits (the axes of choice) + **Verbs** + the Seam it forms.
- **Operations** (how it's governed/consumed): lead with Types/Levels/Rules + **enforcement Verbs**. Note: governance layers are enforcement checkpoints (customs analogy), not swappable sockets.

---

# CLOUD — 3-3-2

## Foundation (domains)

### Compute — *execute the workload*
- **Verbs:** scale out · scale in · scale up/down · pre-warm · queue · shed/throttle load · degrade gracefully · fail fast · bin-pack
- **Failure modes:** cold-start lag · saturation / CPU throttle · queue overflow · capacity exhaustion · thundering herd
- **Elements:** serverless/FaaS · containers/orchestration · managed runtime · VM · horizontal/vertical autoscaling · warm pool · spot capacity
- **Quality attributes:** elasticity · scalability · latency · throughput · cold-start behaviour · deployability
- **Seam:** compute abstraction boundary (serverless↔container↔VM) · build↔run boundary
- **Decisions:** compute model per workload · autoscaling policy · state placement · commitment model (reserved/on-demand/spot)
- **Trade-offs:** elasticity vs cold-start latency · fine-grained scaling vs coordination cost · managed velocity vs lock-in

### Storage — *persist and retrieve state durably*
- **Verbs:** persist · retrieve · replicate · tier / age-out · snapshot · version · encrypt-at-rest · serve-stale
- **Failure modes:** replication lag · stale read · tier-retrieval delay · capacity exhaustion · corruption
- **Elements:** object store · block/disk · file · hot/warm/cold tiering · replication · snapshots
- **Quality attributes:** durability · availability · consistency · retrieval latency · capacity
- **Seam:** storage-compute separation · archive/tiering boundary
- **Decisions:** storage class per data · replication topology · consistency model · retention/tiering policy
- **Trade-offs:** consistency vs availability · storage cost vs retrieval latency · durability vs cost

### Network — *move bytes between components and to/from users*
- **Verbs:** route · balance · segment/isolate · cache-at-edge · throttle · fail-over DNS · terminate TLS
- **Failure modes:** partition · path saturation · DNS failure · asymmetric routing · egress-cost blowout
- **Elements:** VPC/subnets · peering · transit gateway · private link · CDN/edge · DNS · load balancer · egress control
- **Quality attributes:** latency · throughput · path availability · segmentation security
- **Seam:** network perimeter (VPC/private link/segmentation) · edge↔origin boundary
- **Decisions:** network topology · egress path · segmentation model · edge/CDN placement
- **Trade-offs:** segmentation strictness vs connectivity simplicity · edge cache freshness vs origin load

## Decisions (categories)

### Integration — *how services talk*
- **Intent:** connect components without hardwiring them.
- **Traits (axes of choice):** sync vs async · orchestration vs choreography · pub/sub vs queue vs request-reply
- **Verbs:** publish · subscribe · enqueue · retry-with-backoff · dead-letter · break-circuit · apply-backpressure · fan-out/fan-in
- **Elements:** event bus · queue · API gateway · service mesh · circuit breaker · DLQ · saga/outbox
- **Seam:** sync↔async boundary · event schema/registry boundary · API gateway/public contract boundary
- **Trade-offs:** sync simplicity vs async resilience · orchestration control vs choreography autonomy

### Resilience — *how it survives failure*
- **Intent:** keep the system serving, or degrading acceptably, through failures.
- **Traits:** RTO/RPO tiering · blast-radius containment · graceful degradation
- **Verbs:** fail-over · fall-back · degrade · roll-back · isolate (bulkhead) · retry · replay
- **Elements:** multi-AZ/region · active-active/passive · blue-green/canary · chaos · DR tiers (backup-restore / pilot-light / warm / hot standby)
- **Seam:** control plane↔data plane · region/AZ boundary
- **Trade-offs:** redundancy cost vs RTO/RPO · availability vs consistency

### Security — *who/what is allowed*
- **Intent:** enforce identity, confidentiality, integrity, least privilege.
- **Traits:** zero-trust · defense-in-depth · shared responsibility
- **Verbs:** authenticate · authorize · encrypt · rotate · segment · deny/quarantine · audit
- **Elements:** IAM/workload identity · SSO/OIDC/SAML · secrets/KMS · mTLS · segmentation · WAF · scoped short-lived creds
- **Seam:** identity/trust boundary · shared-responsibility line at each managed service
- **Trade-offs:** security restrictiveness vs developer velocity

## Operations

### Cost
- **Types:** unit economics · commitment models (reserved/on-demand/spot)
- **Levels:** per-transaction · per-tenant · per-environment
- **Rules/conditions:** tagging · budget alerts · showback/chargeback · anomaly detection
- **Enforcement verbs:** right-size · reserve · tier-lifecycle · alert-on-budget · throttle-by-budget
- **Trade-offs:** commitment discount vs flexibility · cost predictability vs elasticity

### Governance
- **Types:** landing zones · tenancy · tagging · approval gates
- **Levels:** org / account / subscription / resource
- **Rules/conditions:** policy-as-code · residency constraints · guardrails
- **Enforcement verbs:** enforce-policy · gate-approval · tag · quarantine-noncompliant
- Enforcement checkpoint, not a swappable socket.

---

# DATA — 3-1-1

## Foundation (domains)

### Modeled — *give data meaning and structure*
- **Verbs:** normalize · denormalize · resolve-identity · survive (survivorship) · version-schema · promote-grain · link-entities
- **Failure modes:** identity collision · schema-drift break · grain mismatch · model↔semantic divergence
- **Elements:** conceptual/logical/physical · dimensional/vault · grain · surrogate/natural keys · SCD · semantic layer · entity/identity resolution · golden record · MDM · knowledge graph/ontology
- **Quality attributes:** accuracy · consistency · uniqueness · evolvability · interoperability
- **Seam:** physical model↔semantic layer boundary
- **Decisions:** grain of core fact · SCD type per dimension · identity match thresholds · relational vs graph vs hybrid representation
- **Trade-offs:** normalization vs query performance · match precision vs recall

### Stored — *persist analytically*
- **Verbs:** land · promote (raw→curated) · partition · compact · tier · time-travel/restore · materialize
- **Failure modes:** small-file blowup · partition skew · promotion stall · tier-retrieval latency
- **Elements:** warehouse · lake · lakehouse · medallion · open table format · partitioning/clustering · tiering · time-travel · materialized view
- **Quality attributes:** query performance · scalability (volume/velocity/variety) · recoverability
- **Seam:** raw→curated promotion boundary · storage-compute separation
- **Decisions:** table format · partitioning/clustering keys · retention/tiering policy
- **Trade-offs:** freshness vs cost · storage cost vs recompute cost · raw retention vs aggregation

### Moved — *get data from A to B*
- **Verbs:** extract · load · transform · capture-change · replicate · federate · backfill · replay · watermark · dedupe
- **Failure modes:** late-arriving data · out-of-order events · duplicate delivery · replication lag · contract break
- **Elements:** ETL/ELT · batch/micro-batch/streaming · CDC · log-replication · event sourcing · federation/virtualization · reverse-ETL · data contracts · idempotency · backfill/replay/watermark
- **Quality attributes:** timeliness/freshness · completeness · consistency · idempotency
- **Seam:** producer↔consumer data contract · batch-stream convergence point · replication↔federation boundary
- **Decisions:** batch vs streaming per pipeline · replicate or federate per source · contract enforcement point · sync direction & conflict resolution
- **Trade-offs:** latency vs completeness · replication vs federation · pipeline simplicity vs reprocessing flexibility

## Constraint

### Governed — *quality & access*
- **Types:** lineage · catalog · classification · access control · PII treatment · retention · residency
- **Enforcement verbs:** classify · mask/tokenize/encrypt · enforce-RBAC/ABAC · trace-lineage · apply-retention · honor-erasure
- **Failure modes:** lineage break · unmasked PII leak · access over-grant · retention violation
- **Seam:** catalog↔pipeline metadata boundary · regulatory/residency boundary
- **Trade-offs:** centralized governance vs domain autonomy · access restrictiveness vs analyst velocity

## Operations

### Served — *how it's consumed*
- **Types:** semantic layer · API/data access · feature store · embedding store · BI · activation · OLTP/OLAP/HTAP
- **Verbs:** expose · aggregate · unify-profile · segment · activate · serve-feature
- **Seam:** semantic layer↔serving/API boundary
- **Decisions:** semantic layer placement (warehouse / BI tool / API)
- **Trade-offs:** one canonical model vs domain-local models

---

# AI — 2-2-2

*Fewer independent domains because model selection cascades downstream — tightest causal coupling of the three stacks.*

## Foundation (domains)

### Models — *the reasoning engine*
- **Verbs:** infer · route · cascade · stream-tokens · batch · fall-back-to-cheaper · quantize · fine-tune
- **Failure modes:** hallucination · cold-load · capacity exhaustion · quality regression on version change
- **Elements:** foundation/LLM/SLM · multimodal · routing · sizing · fine-tune/LoRA · distillation · quantization · inference server · continuous batching · KV cache
- **Quality attributes:** accuracy · latency (TTFT, e2e) · throughput · cost per interaction · tail-latency stability
- **Seam:** model provider boundary (abstraction/routing) · training↔serving boundary
- **Decisions:** model selection/routing · build vs buy vs fine-tune · inference hosting & capacity model
- **Trade-offs:** accuracy vs latency · quality vs cost per token · general model vs fine-tuned small · provider abstraction vs native features

### Context — *what the model reasons over*
- **Verbs:** retrieve · rank · re-rank · assemble · truncate · ground · cache · cite/attribute
- **Failure modes:** retrieval miss · context noise/overflow · stale index · injection · attribution failure
- **Elements:** retrieval/RAG · chunking · embedding · vector index · hybrid search · re-ranking · grounding · context assembly/truncation · prompt/semantic caching · GraphRAG · ontology-grounded retrieval
- **Quality attributes:** groundedness · faithfulness · relevance · coverage · injection resistance
- **Seam:** context assembly boundary · retrieval boundary (index↔generation) · trust-perimeter boundary (what data leaves)
- **Decisions:** RAG vs fine-tune vs hybrid · chunking/embedding strategy · vector store selection · grounding source of truth (vector index / knowledge graph / structured query / hybrid)
- **Trade-offs:** retrieval recall vs precision & noise · chunk size vs coherence · memory richness vs privacy

## Decisions (categories)

### Agentic — *how it acts*
- **Intent:** let the system plan and take actions, not just answer.
- **Traits:** autonomy level · orchestration topology (single/supervisor/hierarchical/peer) · tool permissions
- **Verbs:** plan · call-tool · delegate · hand-off · reflect · escalate-to-human · recover-from-failure · halt (loop/budget) · fall-back-deterministic
- **Elements:** planner/executor · tool use · memory (short/long, episodic/semantic) · handoff/delegation · HITL · approval gate · step/budget limits · sandbox · deterministic fallback
- **Seam:** tool-call boundary · agent-to-agent handoff · HITL approval gate · agent identity / delegated-authority boundary
- **Decisions:** agent topology · autonomy level & budgets · tool permission scope · approval-gate placement · agent identity model · irreversible-action policy & segregation of duties
- **Trade-offs:** autonomy vs controllability · single-agent simplicity vs multi-agent capability · human oversight vs throughput

### Safety — *bounding behaviour*
- **Intent:** keep outputs and actions within acceptable bounds.
- **Traits:** guardrail placement (pre/in-flight/post) · severity thresholds · eval gates
- **Verbs:** filter · block/refuse · redact · gate-on-eval · escalate · kill/halt
- **Elements:** guardrails · eval harness · golden dataset · LLM-as-judge · regression gate · red-team · injection defense · PII redaction · kill switch · HITL
- **Seam:** guardrail boundary (pre-call/in-flight/post-call) · evaluation boundary (offline harness↔production telemetry)
- **Decisions:** guardrail placement & thresholds · eval metrics & regression thresholds · risk-tier classification
- **Trade-offs:** guardrail strictness vs task completion · evaluation rigor vs delivery speed · determinism vs generative flexibility

## Operations

### Governance
- **Types:** traceability · attribution · rollback · versioning
- **Verbs:** trace · attribute (who caused what) · roll-back · version · audit
- **Seam:** action attribution boundary · prompt/version artifact boundary
- **Decisions:** trace capture/retention · prompt/model versioning & rollback · attribution granularity per agent action

### Observability
- **Types:** traces · spans · drift detection · regression gates · agent trajectory evaluation
- **Verbs:** trace-span · detect-drift · gate-regression · replay · measure-trajectory
- **Quality attributes:** task success rate · containment/deflection rate · tool-call accuracy · behavioural stability across model versions
- **Trade-offs:** eval rigor vs delivery speed

---

## How to study this (Pareto)

1. **Memorize the verbs and failure modes first** — those two columns are what you were missing and what makes S-S-A-E-R-M writable. Everything else you can already partially recall.
2. **Foundation domains before Decision categories before Operations** — the domains are where quality attributes are genuinely testable; decision categories mostly *borrow* forces from the domains they govern.
3. **The cross-stack seams (Topic 2) are where the AI SA role lives** — Data↔AI (ontology/grounding), Cloud↔AI (serving as a workload), the three-way enterprise policy plane. This ontology is the prerequisite for reasoning about those joints.
