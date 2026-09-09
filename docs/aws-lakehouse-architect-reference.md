# AWS Lakehouse — Architect Reference (Combined Artifact)

*Framework discipline: IS / BOUND / DOES with **DOES = full Operations (ASSO)**, not just engine choice — the ontology validated across 300+ recall drills, not the drifted version from any downstream prompt.*

---

## Step 1 — Architecture Design (L99, ELI14, ≤300 words)

**Scenario:** Global retailer unifying POS (batch), CRM (batch), and e-commerce clickstream (real-time) into one Customer 360, needing both real-time marketing triggers and batch BI reporting.

**Gate — Lake Formation:** centralized row/column permissions across brands. *Trade-off:* governance overhead (every query authorized) vs. uncontrolled PII exposure across teams. Chosen because compliance is non-negotiable with multi-brand PII.
- *Layer trade-off:* **Enforcement strictness vs. query latency** — every read pays an authorization hop; loosening it would cut latency but reopen the PII risk.
- *Quality attribute:* **Security & auditability** (dominant), with a measurable secondary hit on **performance** (per-query auth overhead).

**Lake — S3 + Iceberg:** durable storage with snapshots. *Trade-off:* Iceberg's ACID/time-travel metadata costs extra writes vs. raw Parquet's cheaper throughput. Chosen because backfill correctness (reprocessing a bad batch without corrupting Gold) matters more than raw write speed here.
- *Layer trade-off:* **Consistency vs. throughput** — Iceberg snapshots pay a metadata cost on every write; that cost is earned back the first time a bad backfill needs to be rolled back safely.
- *Layer trade-off:* **Openness vs. integration** — Iceberg over Delta chosen because AWS-native Glue Catalog support + team fluency dominated Delta's Databricks-tight integration for this stack.
- *Quality attribute:* **Reliability & recoverability** (dominant, via snapshot isolation), with a measurable secondary hit on **write throughput** (metadata overhead).

**River — Kinesis (real-time) + Glue ETL/Jobs (batch), Silver→Gold modeling:** *Trade-off:* Kinesis's continuous shard cost vs. batch-only cheaper-but-stale. Chosen because cart-abandonment triggers need sub-minute freshness; POS/CRM tolerate next-day batch.
- *Layer trade-off:* **Cost vs. freshness** — Kinesis only where a sub-minute SLA is genuinely required by a business use case; batch everywhere else to avoid paying for freshness nobody consumes.
- *Layer trade-off:* **Ingestion complexity vs. per-source correctness** — one unified streaming path would be simpler to operate but would force batch-shaped sources into a streaming model they don't need.
- *Quality attribute:* **Timeliness/latency** (dominant, on the Kinesis leg) balanced against **cost-efficiency** (dominant, on the Glue batch leg) — the layer deliberately optimizes both by splitting workloads, not by picking one.

**Engine — EMR (identity resolution) + Athena (analyst BI):** *Trade-off:* EMR's fixed cluster cost vs. Athena's per-query cost. EMR wins for the heavy, sustained join-workload (identity resolution); Athena wins for spiky, unpredictable analyst queries where idle capacity should cost nothing.
- *Layer trade-off:* **Fixed vs. per-query cost** — EMR for sustained heavy joins where amortized cluster cost beats per-query pricing; Athena for spiky ad-hoc where idle cost should be zero.
- *Layer trade-off:* **Operational simplicity (one engine) vs. cost-fit (right engine per workload)** — chose the latter; forcing one engine would pay the wrong cost curve on half the workload.
- *Quality attribute:* **Cost-efficiency** (dominant, via workload-appropriate engine choice) with a measurable secondary hit on **operability** (two engines to monitor, tune, and staff for).

**Full Operations (DOES) named, not just engines:** *Activate* — EMR/Athena execute queries. *Scale* — Kinesis shard auto-scaling, EMR cluster autoscaling. *Served* — Athena/BI layer. *Optimized* — Iceberg compaction + Glue job bookmarking to avoid reprocessing unchanged files.

---

## Step 2 — Single Record Trace (COT, ELI14, ≤200 words)

### Trace A — Cart-add event (real-time path)

A shopper adds an item to cart:

1. **Clickstream event fires → published to a named Kinesis Data Stream shard** (e.g., `clickstream-events`, River, `*moved`/`*ingested`). The event physically lives inside that stream's shard for the retention window (24h default, up to 365d), not in a database table yet — the stream is the buffer, not the store.
2. **Glue Streaming enriches it with a CRM customer ID → lands in the Bronze Iceberg table** (physically: Parquet files under an S3 prefix such as `s3://<bucket>/bronze/clickstream/`, registered as a Bronze table in the AWS Glue Data Catalog). A new Iceberg snapshot is written; the old state isn't overwritten, so it's recoverable.
3. **Lake Formation checks permissions (Gate) when the EMR identity-resolution job later reads it** — column-level PII masking applied before any downstream join happens.
4. **EMR resolves identity, merges into the Gold customer-360 table** (physically: Iceberg-formatted Parquet files at `s3://<bucket>/gold/customer_360/`, registered as a Gold table in the AWS Glue Data Catalog — same catalog Lake Formation governs and Athena/EMR query against). River completes the Modeled step; the merge writes a new Iceberg snapshot, not an in-place overwrite.
5. **Analyst queries Gold via Athena** (Engine, `*activates` + `*served`). Athena reads the Gold table's Iceberg metadata via the AWS Glue Data Catalog and scans only the current-snapshot Parquet files on S3 — there's no separate database server; the "table" the analyst sees is the Glue Catalog registration pointing at S3.
6. **Lake Formation authorizes again, at query time** — the Gate isn't a one-time ingest check; it re-fires per query, which is why it's drawn as governing every layer, not just the entry point.

### Trace B — Loyalty signup event (source to query)

A customer signs up for the loyalty program via the mobile app:

1. **Signup event → Kinesis Data Stream (River, `*moved`/`*ingested`).** The loyalty app publishes the event live so downstream personalization can fire within seconds — not tomorrow morning's batch.
2. **Kinesis Firehose buffers and lands the raw event in the Bronze Iceberg table** (physically: Parquet files under `s3://<bucket>/bronze/loyalty_signup/`, registered in the AWS Glue Data Catalog). The River's first job is *move-and-land-safely*; no modeling yet, so nothing has to be "right" here beyond durability.
3. **Glue Job (Silver, every 15 min) validates the row** — schema check, PII flags — then joins against the CRM master to attach the canonical customer ID and writes to the **Silver Iceberg table** (physically: `s3://<bucket>/silver/loyalty_signup/`, registered as a Silver table in the AWS Glue Data Catalog). River layer, `*validated` + `*modeled`.
4. **Nightly Glue Job aggregates Silver → Gold customer-360 table** (Lake, `*modeled`). The Gold table physically lives as **Iceberg-formatted Parquet files on S3**, registered as a **table in the AWS Glue Data Catalog** (the same catalog Lake Formation governs and Athena/EMR query against — no separate database engine). Iceberg snapshot makes the rebuild atomic — any query mid-rebuild still sees the previous snapshot cleanly.
5. **A marketing analyst queries "signups in the last 24h by region" via Athena** (Engine, `*activates` + `*served`). Athena because it's one ad-hoc SQL — pay-per-query fits; EMR would be wrong shape.
6. **Lake Formation authorizes at query time** (Gate, `*govern`), masks the analyst's role from raw PII columns, returns the aggregated result.

Same 4-layer path as Trace A, but different freshness contract (15-min Silver vs. sub-minute cart-abandonment) — proving the architecture serves both without duplicating the stack.

---

## Step 3 — STAR Story (Principal Data Architect framing)

### Header
**Title:** Real-Time Customer 360 — Multi-Brand Retail Data Unification
**Competencies:** Architecture Governance & Access Control (#1), Storage Design & Consistency Trade-offs (#2), Real-Time + Batch Hybrid Ingestion (#3), Compute Engine Selection & Cost Governance (#4), Data Modeling & Identity Resolution (#5)
**Platform stack:** AWS — Lake Formation, S3, Iceberg, Kinesis, Glue ETL/Jobs, EMR, Athena

### Situation (≈100 words)
A global multi-brand retailer needed a unified Customer 360 combining POS transactions, CRM records, and e-commerce clickstream — three sources with different arrival cadences (batch, batch, real-time) and no shared identity key. Marketing needed sub-minute cart-abandonment triggers; finance needed reliable next-day batch reporting from the same underlying customer entity. Compliance required strict, auditable, column-level PII controls across brands sharing infrastructure but not data access. No existing platform reconciled real-time and batch consistently against one governed identity model.

### Task (≈50 words)
I owned the architecture: the storage format, the ingestion topology, the compute-engine split, and the governance model — including the consistency-vs-freshness and cost-vs-latency trade-offs behind each. Engineers implemented Glue jobs and EMR workflows against my design; I was accountable for whether the architecture held under real load and audit, not for writing the pipeline code myself.

### Action

1. **Selected Iceberg-on-S3 over raw Parquet** after personally reviewing our backfill failure logs from a prior program — confirmed that uncontrolled reprocessing had silently corrupted a Gold table before; Iceberg's snapshot isolation was the direct fix. *(Self-verified evidence, not a relayed summary.)*
2. **Split real-time vs. batch ingestion (Kinesis vs. Glue)** as a deliberate freshness-vs-cost trade-off — Kinesis's continuous shard cost was only justified for the one workload (cart-abandonment) with a sub-minute SLA; everything else stayed batch to avoid paying for freshness nobody needed.
3. **Chose EMR over Athena for identity resolution specifically**, a cost-vs-elasticity trade-off — modeled the join volume myself and confirmed a fixed EMR cluster was cheaper than per-query Athena pricing at that sustained volume; kept Athena for the unpredictable analyst-query workload where idle cost should be zero.
4. **Designed column-level masking in Lake Formation** so PII access was enforced identically whether the reader was a real-time marketing service or a batch analyst — one governance model, not two.
5. **Mandated re-authorization at query time, not just ingest time** — closed a gap where an early design only checked permissions on write, which would have let a later, broader read bypass masking.
6. **Built the Silver→Gold identity-resolution step to write back through Iceberg**, not overwrite in place — any bad merge could be rolled back to the prior snapshot without a full reprocess.

### Result (2–4 sentences)
Sub-minute marketing triggers went live without compromising the batch reporting SLA on the same underlying data. Zero PII masking failures across a subsequent compliance audit — verified against the query-time re-authorization design specifically. The Iceberg-based rollback pattern was adopted as the standard recovery mechanism for future ingestion programs.

### QA Drills

- **"Why Iceberg over plain Parquet?"** → Snapshot isolation gave us rollback after a prior corrupted-backfill incident — verified from our own failure logs, not a vendor recommendation.
- **"Why split Kinesis and Glue instead of streaming everything?"** → Freshness costs money; only the cart-abandonment use case justified it.
- **"Why EMR for identity resolution but Athena for analysts?"** → Sustained heavy joins are cheaper fixed; spiky ad-hoc queries are cheaper serverless.
- **"How is governance enforced, not just designed?"** → Lake Formation checks fire at query time, every time — not only at ingest.
- **"What would make you abandon this design?"** → If real-time SLA needs expanded beyond one use case, Kinesis's shard cost would need re-justifying against a full streaming-lakehouse redesign.

---

## Mnemonic Block — Internalize & Recall

Customized to this artifact's exact structure (three tiers, four layers, one honest engine split) — not a generic recall aid.

### The three-tier recital (say in one breath, in order)

1. **Axes** — *IS / BOUND / DOES* → asks *what data is / what limits it / what the system does*.
2. **Tags** — *SMM·VI · GVT·TRI · ASSO*
   - **SMM·VI** = Stored, Moved, Modeled · Versioned, Ingested
   - **GVT·TRI** = Govern, Validate, Trace (policy) · Topology, Resident, Isolated (placement)
   - **ASSO** = Activate, Serve, Scale, Optimize
3. **Layers** — *"Gate → Lake → River → Engine"* (spatial walk) or *"Catalog governs, Lake stores, Pipelines move, Spark/compute activates"* (literal). Pick one telling per recital, never both interleaved.

### The one-liner devices

- **Structure spine:** *"Data IS BOUND to DO something."*
- **Layer spatial walk:** *"The Gate guards the Lake, the River feeds it, the Engine drinks from it."*
- **Layer acronym:** **G-L-R-E** (rhymes with "glory" minus the 'o').
- **Engine choice (this artifact's honest asymmetry):** *"If SQL fits, Athena sits. If Spark must park, EMR embarks."*
- **AWS-specific caveat:** *"There is no one Engine on AWS — you pick per workload."*
- **Policy vs. placement:** *"Policy is a GVT, placement is a TRI."*

### AWS service anchors — one per layer

| Layer | AWS anchor | Tag(s) it realizes | Trade-off / Quality attribute |
|---|---|---|---|
| Gate | Lake Formation | `*govern` | Enforcement strictness vs. query latency / Security & auditability |
| Lake | S3 + Iceberg (snapshots) | `*stored`, `*versioned` | Consistency vs. throughput · Openness vs. integration / Reliability & recoverability |
| River | Kinesis / Glue ETL / Glue Jobs | `*moved`, `*ingested`, `*modeled` | Cost vs. freshness / Timeliness · Cost-efficiency |
| Engine | Athena \| EMR (pick per workload) | `*activates`, `*served` | Fixed vs. per-query cost / Cost-efficiency vs. operability |

### Trade-off pairs to carry into any AWS lakehouse conversation

- **Cost vs. freshness** → Kinesis only where sub-minute SLA is real; batch everywhere else.
- **Openness vs. integration** → Iceberg over Delta when AWS-native + team fluency dominates.
- **Fixed vs. per-query cost** → EMR for sustained heavy joins; Athena for spiky ad-hoc.
- **Consistency vs. throughput** → Iceberg snapshots pay a metadata cost; earn it back on backfill safety.

### Daily drill order (five clauses, one breath each)

**Axes → Tags → Layers → AWS anchors → Engine-choice line.**

Add the trade-off pairs only on days you have time for the sixth clause; the first five are the non-negotiable daily rep.

---

*Verification note: AWS service capabilities (Lake Formation ABAC scope, Iceberg-on-Glue feature parity with Delta, Athena engine v3 changes) evolve — verify against current AWS docs before this becomes a build reference.*
