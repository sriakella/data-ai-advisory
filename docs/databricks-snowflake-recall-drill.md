# Databricks & Snowflake Recall Drill

Same three axes, same acronym scaffolds you already own (SMM·VI, GVT·TRI, ASSO) — this time every answer must be a **named Databricks feature**, not the abstract tag. If you can only say "`*validated`" and not "DLT Expectations," this axis isn't internalized for Databricks yet.

---

## The scaffold, Databricks-flavored

| Axis | Question | Tag | Databricks component |
|---|---|---|---|
| **Domain** | what IS it | `*stored` | Delta Lake on cloud object storage (S3/ADLS/GCS) — the `_delta_log` |
| | | `*versioned` | Delta **Time Travel** (`VERSION AS OF`), checkpoint + JSON commit log |
| | | `*moved` | **Auto Loader** (`cloudFiles`) / Structured Streaming / shuffle |
| | | `*ingested` | Auto Loader incremental file-state tracking |
| | | `*modeled` | **Gold** Delta tables, materialized views, DLT/Lakeflow Silver→Gold |
| **Constraint — policy** | who may TOUCH | `*govern` | **Unity Catalog** — row filters, column masks, credential vending |
| | | `*validated` | **DLT / Lakeflow Expectations** — 3 modes: warn / drop row / fail update |
| | | `*traced` | UC **lineage** graph + system tables (audit logs) |
| **Constraint — placement** | WHERE it sits | `*topology` | Control-plane vs data-plane split; classic (your VPC) vs serverless |
| | | `*resident` | Regional workspace + regional UC metastore |
| | | `*isolated` | Private Link / private endpoints, secure cluster connectivity (no public IP) |
| **Operation** | what it DOES | `*activates` | **Apache Spark + Photon** (vectorized C++ engine) |
| | | `*served` | **Databricks SQL** warehouses, Mosaic AI / Model Serving |
| | | `*scaled` | Cluster autoscaling, DLT scale-out, serverless pools |
| | | `*optimized` | `OPTIMIZE`, auto-compaction, **Liquid Clustering**, `VACUUM`, deletion vectors |

---

## Drill 1 — Blind fill (cover the right column, cold)

Answer with the **Databricks feature name**, not the tag.

1. What gives Delta Lake ACID guarantees on top of raw object storage?
2. What lets you query a table exactly as it existed yesterday at 3pm?
3. What incrementally ingests new files landing in cloud storage without re-scanning the bucket?
4. What are the three enforcement modes for a data-quality rule in a DLT pipeline, and what's the *default*?
5. What intercepts a SQL query *before* Spark reads a file, to apply a row filter or column mask?
6. What Databricks object tells you the full Bronze→Gold lineage of a Gold table, automatically?
7. Name the two deployment models for where compute physically runs.
8. What keeps a workspace's data and metastore inside a specific geographic region?
9. What connectivity pattern keeps worker VMs unreachable from the public internet?
10. What's the vectorized execution engine that accelerates Spark, and what physically-different failure mode does it help avoid on shuffle spill?
11. What lets a DLT pipeline add worker VMs when volume spikes and remove them after?
12. Name three distinct maintenance operations that fight the Small File Problem.

---

## Drill 2 — Reverse direction (feature → axis)

Given the Databricks feature, name the **axis + tag** it belongs to. This is the harder direction — it's what a live conversation actually demands, since someone hands you a feature name, not a tag.

1. Liquid Clustering →
2. Auto Loader →
3. Secure cluster connectivity (no public IP) →
4. DLT Expectations set to `ON VIOLATION FAIL UPDATE` →
5. Unity Catalog credential vending →
6. Delta Time Travel →
7. Serverless SQL warehouse autoscaling →
8. Access History / Object Dependencies (UC lineage) →
9. Photon engine →
10. Regional Unity Catalog metastore →

---

## Drill 3 — The one-sentence defense (interview-shape)

For each, answer in one breath — if it takes two, it's not internalized:

1. **Why does Delta Lake need a transaction log at all, when Parquet files already exist in the bucket?**
2. **Why is the DLT Expectations *default* (warn) a trap if you assume it means "bad data gets dropped"?**
3. **Why can't Unity Catalog governance alone answer a residency question ("is this data in-region")?**
4. **Why does Photon reduce shuffle-spill pain without literally "bypassing" EBS, as commonly overstated?**
5. **What's the actual difference between a streaming table and a materialized view in DLT/Lakeflow, and when do you pick each?**

---

## Answer key

<details>
<summary>Drill 1</summary>

1. The `_delta_log` — JSON-backed ACID transaction log with periodic Parquet checkpoints
2. Delta Time Travel (`VERSION AS OF` / `TIMESTAMP AS OF`)
3. Auto Loader (`cloudFiles`)
4. Warn (default, retains row) / Drop Row / Fail Update — **default is Warn**, which retains the bad row
5. Unity Catalog — rewrites the Spark execution plan before any file read
6. Unity Catalog lineage graph
7. Classic (compute in your VPC/VNet) and Serverless (compute in Databricks' account)
8. Regional workspace deployment + regional UC metastore
9. Secure cluster connectivity / no public IP, over Private Link
10. Photon — vectorized, off-heap execution; reduces *frequency and volume* of spill, doesn't literally reroute I/O via memory-mapping
11. DLT/Lakeflow autoscaling
12. `OPTIMIZE` (bin-packing), auto-compaction / optimized writes, Liquid Clustering, deletion vectors (merge-on-read avoids rewrite churn)

</details>

<details>
<summary>Drill 2</summary>

1. Operations → `*optimized`
2. Domain → `*moved` / `*ingested`
3. Constraint (placement) → `*isolated`
4. Constraint (policy) → `*validated`
5. Constraint (policy) → `*govern`
6. Domain → `*versioned`
7. Operations → `*scaled`
8. Constraint (policy) → `*traced`
9. Operations → `*activates`
10. Constraint (placement) → `*resident`

</details>

<details>
<summary>Drill 3 — reasoning shape, not verbatim answers</summary>

1. Object storage has no file-locking, no atomicity, no index — the log is what turns a folder of Parquet files into a transactional table.
2. Warn retains the bad row and only logs a metric — if you assumed "violation = dropped," ungoverned data is still flowing into Gold.
3. Revoking access doesn't move a single byte across a border — access (who) and residency (where) are orthogonal controls.
4. The NVMe benefit depends on instance-family selection, not automatic behavior; Photon's real contribution is vectorized memory management that reduces how often you spill.
5. Streaming tables are incremental/append/CDC (good for Bronze/Silver); materialized views recompute or incrementally refresh a full query (good for Gold aggregates) — picking wrong is a common cost/correctness trap.

</details>

---

## Scoring rule

- **Drill 1 (forward recall):** 12/12 cold = axis→feature mapping is solid.
- **Drill 2 (reverse):** this is the one that predicts conversational fluency — score it separately, and if it lags Drill 1, that's your signal you know the list but not the lens.
- **Drill 3:** no partial credit — either you can defend it in one breath or you can't yet. Anything that takes two breaths goes back into rotation tomorrow.

Run all three cold, in this order, once before checking answers. Log misses by *type* (feature name forgotten vs. wrong axis vs. no defense) — the type tells you which drill to repeat, not just that you missed.

---
---

## Snowflake Recall Drill — Domains / Constraints / Operations

Same three axes, same acronym scaffolds — every answer here must be a **named Snowflake feature**. Where Snowflake's mechanism genuinely differs from Databricks' (e.g. `*validated` is a soft spot; `*topology`/`*isolated` are managed rather than architected), the drill calls it out rather than forcing a false parallel.

---

### The scaffold, Snowflake-flavored

| Axis | Question | Tag | Snowflake component |
|---|---|---|---|
| **Domain** | what IS it | `*stored` | Micro-partitioned columnar storage (FDN format) on the cloud object store |
| | | `*versioned` | **Time Travel** + **Fail-safe** |
| | | `*moved` | **Snowpipe** / Snowpipe Streaming, **Streams** (CDC), Secure Data Sharing |
| | | `*ingested` | Snowpipe / Snowpipe Streaming continuous file ingestion |
| | | `*modeled` | **Dynamic Tables**, materialized & standard views |
| **Constraint — policy** | who may TOUCH | `*govern` | RBAC + **masking policies** + **row access policies** |
| | | `*validated` | *Soft spot* — no first-class Expectations equivalent; covered via dbt tests / Dynamic Table constraints |
| | | `*traced` | **Access History**, Object Dependencies (Horizon governance) |
| **Constraint — placement** | WHERE it sits | `*topology` | Single managed control/compute plane — *configured*, not architected |
| | | `*resident` | Region placement + replication/failover scoping |
| | | `*isolated` | **Network policies**, PrivateLink / private connectivity |
| **Operation** | what it DOES | `*activates` | **Virtual Warehouses** (T-shirt sized compute) |
| | | `*served` | Warehouses serving queries, **Cortex** (AI functions), Snowsight |
| | | `*scaled` | **Multi-cluster warehouses**, auto-suspend/resume, per-second billing |
| | | `*optimized` | Automatic micro-partition pruning, optional auto-clustering, search optimization service |

---

### Drill 1 — Blind fill (cover the right column, cold)

1. What storage format gives Snowflake automatic pruning without manual partitioning?
2. What lets you query a table as it existed before an accidental `DELETE`, and what's the fallback if Time Travel's window has expired?
3. What continuously loads files landing in a stage without a running warehouse?
4. What captures row-level change data for downstream consumption?
5. What declarative object automatically refreshes a transformed table as its sources change?
6. What two policy types enforce column-level and row-level security, and which is intercepted at query-plan time?
7. What Snowflake capability is the closest analog to DLT Expectations — and why is it *not* first-class?
8. What object graph shows you which tables and views a given table depends on?
9. What connectivity option keeps Snowflake traffic off the public internet?
10. What's billed per-second and auto-suspends when idle?
11. What feature lets a single logical warehouse absorb a concurrency spike by adding clusters?
12. Name the mechanism that lets a query skip irrelevant micro-partitions without you writing a clustering key.

---

### Drill 2 — Reverse direction (feature → axis)

1. Time Travel + Fail-safe →
2. Masking policy →
3. Snowpipe Streaming →
4. Multi-cluster warehouse auto-scale →
5. Access History →
6. Dynamic Tables →
7. Network policy / PrivateLink →
8. Region-scoped replication →
9. Virtual warehouse (query execution) →
10. Automatic micro-partition pruning →

---

### Drill 3 — The one-sentence defense (interview-shape)

1. **Why is `*validated` the honest gap when mapping Snowflake to this framework, compared to Databricks?**
2. **Why do `*topology` and `*isolated` shrink to "configuration" on Snowflake rather than "design," the way they do on Databricks?**
3. **Why does a masking policy not answer a data-residency question, even though both are "governance"?**
4. **What's the practical difference between Time Travel and Fail-safe, and why does that difference matter operationally?**
5. **Why is a multi-cluster warehouse a `*scaled` concern and not an `*activates` concern, even though both involve compute?**

---

### Answer key

<details>
<summary>Drill 1</summary>

1. Micro-partitioned FDN columnar format — metadata-driven pruning is automatic, no manual partition design
2. Time Travel (`AT`/`BEFORE` queries, default up to 90 days on Enterprise+); Fail-safe (7-day, Snowflake-recovery-only, no self-serve query)
3. Snowpipe (serverless, event- or REST-triggered ingestion)
4. Streams (CDC on a table, consumed by a Task or Dynamic Table)
5. Dynamic Tables — declarative, target-lag-driven refresh
6. Masking policies (column) and row access policies (row) — both intercepted at query-plan time, before data is returned
7. Nothing first-class exists — quality gating leans on dbt tests or hand-built Dynamic Table constraints; this is the framework's flagged soft spot
8. Object Dependencies (part of Horizon governance)
9. Network policies + PrivateLink (or equivalent private connectivity)
10. Virtual warehouse compute
11. Multi-cluster warehouse (auto-scale policy)
12. Automatic micro-partition pruning via stored min/max metadata

</details>

<details>
<summary>Drill 2</summary>

1. Domain → `*versioned`
2. Constraint (policy) → `*govern`
3. Domain → `*moved` / `*ingested`
4. Operations → `*scaled`
5. Constraint (policy) → `*traced`
6. Domain → `*modeled`
7. Constraint (placement) → `*isolated`
8. Constraint (placement) → `*resident`
9. Operations → `*activates`
10. Operations → `*optimized`

</details>

<details>
<summary>Drill 3 — reasoning shape, not verbatim answers</summary>

1. Databricks has DLT/Lakeflow Expectations as a first-class, three-mode quality gate; Snowflake has no equivalent object — quality has to be bolted on, so the cell is genuinely thinner, not just differently named.
2. Snowflake manages the control/compute separation and much of the network fabric itself — you configure region and network policy, but you don't design a VPC/VNet the way you do on Databricks classic compute.
3. Masking controls *who* can see a value; residency controls *where the bytes physically exist* — a fully masked table can still be sitting in the wrong region, and a correctly-resident table can still be unmasked.
4. Time Travel is self-serve and queryable by you; Fail-safe is a Snowflake-only recovery window with no user-facing query access — operationally, if you're past Time Travel's retention, you need a support ticket, not a query.
5. `*activates` is about compute *executing* a workload; `*scaled` is about compute *elasticity under changing load* — a single warehouse activates a query, multi-cluster scaling is a separate decision about concurrency headroom.

</details>

---

### Scoring rule

Same rule as the Databricks drill — score Drill 2 separately from Drill 1, since reverse-mapping predicts live-conversation fluency better than forward recall. Drill 3, question 1 is the one to over-index on: knowing Snowflake's `*validated` gap cold is the single most interview-relevant fact in this section, because it's the question most likely to expose someone who's memorized feature names without understanding what's actually missing.

> Note: Snowflake feature names (Horizon, Cortex, Dynamic Tables) evolve quickly — verify current-state capabilities against vendor docs before this is used as a build or interview reference.
