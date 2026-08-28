# DCO → AWS Study Notes — Techwave Data Architect (C115350)

---
## Scope Depth Ladder — Techwave Interview Prep (Rungs 1–13, 15–17, 19–20, 23)

### Drill to reflex — in-person will probe here (Rungs 1–12)
JD anchor: "design scalable, reliable, future-ready data architectures"

1. **Tags** — SMM·VI / GVT·TRI / ASSO
2. **Layer-map** — Gate → Lake → River → Engine
3. **Service** — AWS/Databricks/Snowflake product name per layer
4. **Quality attribute** — dominant "-ility" the layer optimizes for
5. **Trade-off** — what you give up to get that attribute
6. **Layer vs. Config** — actual parameter realizing the trade-off (DPU cap, TARGET_LAG, workgroup limit)
7. **Observability/proof** — metric or log confirming the config is working (CloudWatch, Unity Catalog audit logs, QUERY_HISTORY)
8. **Performance tuning (second-order)** — compaction, shuffle partitions, warehouse sizing under concurrency
9. **Cost mechanics** — per-DPU-hour, per-TB-scanned, per-credit pricing model behind the trade-off
10. **Blast radius / dependency chain** — what breaks downstream if config drifts or is misconfigured
11. **Migration/evolution path** — schema evolution, partition evolution, MERGE SCHEMA
12. **Multi-region/DR posture** — how config shifts under residency or disaster-recovery requirements

### Know cold (Rung 13)
JD anchor: "define data standards, patterns, and reference architectures"

13. **Reference architecture governance** — reusable patterns/standards other teams conform to

### Prep through presales/RFP frame (Rung 15)
JD anchor: Techwave is a consulting firm; RFP/RFI listed as primary skill

15. **Portfolio-level TCO / build-vs-buy** — cost comparison across AWS vs. Databricks vs. Snowflake for client proposals

### Conversational-ready, not owned (Rungs 16, 17, 19, 20)
JD anchors: "review and guide teams" / "align with business + enterprise governance" / "future-ready"

16. **Architecture review board** — review/guide cadence (JD says review and guide, not own)
17. **Regulatory mandate translation** — SR 11-7/MRM, GDPR, PCI-DSS as architectural standards
19. **Multi-year technology roadmap** — what's adopted, piloted, retired platform-wide
20. **Executive/board-level business case** — CFO/CIO-language framing (Layer 1 at max altitude)

### One-hour prep only — high-value if RK pulls here (Rung 23)
JD anchor: "future-ready" + your Bedrock/Agentforce positioning

23. **AI-era data architecture** — vector stores, feature stores, RAG grounding pipelines,
    agent-accessible data surfaces, model+data lineage as one audit chain


---
# DCO → AWS Study Notes — Techwave Data Architect (C115350)

---

## Architects Playbook — Tell · Why · Prove (with 10 Moves nested inside)

RK asks a scenario. You lead the conversation through three arcs — **Tell → Why → Prove** — firing the 10 moves progressively as he pulls each thread. Dialogue, not monologue.

### Tier 1 — TELL (opening arc)

*RK's cue: scenario given. Your intent: name the room you're in and the attribute that dominates.*

| Move | What fires | Note |
|---|---|---|
| **0** | **Architecture pattern** (Monolith / Serverless / Event-driven / Lambda / Kappa / Medallion) | Silent — name it in your head; only speak if scenario is explicitly pattern-shaped |
| **1** | **Layer** (Gate / Lake / River / Engine) | Always fires |
| **2** | **Service** (AWS anchor per layer) | Always fires |
| **3** | **QA** (dominant one-word attribute) | Always fires |

### Tier 2 — WHY (justification arc)

*RK's cue: "Why?" or "Why not X?". Your intent: show the tension you resolved and the parameter that resolves it.*

| Move | What fires |
|---|---|
| **4** | **Trade-off** (both sides of the tension) |
| **5** | **Config** (the parameter realizing the trade-off) |

### Tier 3 — PROVE (evidence arc)

*RK's cue: "How do you know it works?" / "Have you done this?". Your intent: evidence, lived engagement, unprompted guardrail.*

| Move | What fires |
|---|---|
| **6** | **Artifact** (observability/proof — CloudTrail Lake, CloudWatch, DQ report) |
| **7** | **Use case** (real engagement: S&P Global / L'Oréal / BFSI) |
| **8** | **Best practice** (unprompted guardrail from lived experience) |

### Twist follow-up (Move 9)

*RK's cue: unscripted probe after Tier 3. Your intent: name the cluster mentally, deliver the one-line answer.*

| Move | What fires |
|---|---|
| **9** | **Twist cluster hit** (SILC / MCC / CBC — see Compression Mnemonic below) |

---

**Rule:** if RK doesn't ask "why?", don't volunteer Tier 2. If RK doesn't ask "prove it?", don't volunteer Tier 3. Withholding until pulled is what makes it a conversation. Move 0 is *silent* — name the pattern in your head to set framing; only speak it if RK's scenario is explicitly pattern-shaped.

---

## Pillar 1: Data Domains (IS) — What data is, where it lives, how it moves

### DCO Anchor

Three domain verbs: **Stored · Moved · Modeled**, qualified by two state tags: **Versioned · Ingested**.
Mnemonic: **SMM·VI**. The Lake Formation catalog is the domain registry that binds all three.

---

### 1A. Stored / Versioned → S3 + Iceberg

**What it is.** The persistence tier — data at rest with full version history.

**AWS services.**

- **Amazon S3 (general-purpose buckets)** — object store; stores Parquet/ORC data files and Iceberg metadata (manifest lists, manifests, snapshot JSON). Versioning enabled at the bucket level gives object-level recovery; lifecycle rules move cold snapshots to Glacier/Deep Archive.
- **Amazon S3 Tables (table buckets)** — launched Dec 2024, GA and expanding regions through 2026. Purpose-built bucket type that *is* an Iceberg table: built-in Iceberg REST Catalog endpoint, automatic compaction, snapshot management, and unreferenced file removal with no external scheduler. Up to 3× faster query throughput and 10× higher TPS vs. self-managed Iceberg on general-purpose S3. Table-level access control is native (no per-object bucket policies).
- **Apache Iceberg (table format)** — open standard for ACID transactions, row-level updates/deletes, schema evolution, partition evolution, and time-travel queries via queryable snapshots. S3 Tables expose the Iceberg REST Catalog API; any Iceberg-compatible engine (Spark, Trino, Athena, Redshift, Flink) can read/write without vendor lock-in.

**Interview-ready facts.**

- S3 Tables REST Catalog APIs shipped Mar 2025 — engines can `CREATE`, `UPDATE`, `LIST`, `DELETE` tables directly in a table bucket.
- S3 Intelligent-Tiering on table buckets can reduce storage costs by up to 80%.
- For "Versioned" in DCO: Iceberg snapshot isolation gives you point-in-time queries without S3 object versioning — they are separate mechanisms serving different purposes (object recovery vs. table time-travel).
- **Quality attribute — Reliability & recoverability** (dominant): Iceberg snapshot isolation enables rollback after a bad backfill without corrupting downstream tables.
- **Trade-off — Consistency vs. write throughput:** Iceberg metadata cost on every write; earned back the first time a bad batch needs safe rollback.
- **Trade-off — Openness vs. integration:** Iceberg (open standard, AWS-native Glue Catalog support) vs. Delta (tighter Databricks coupling); choose per stack and team fluency.
- **Trade-off — Storage cost vs. query performance:** S3 Intelligent-Tiering cuts cost up to 80% on cold data, but hot-tier scan speed matters for latency-bound queries.

**DCO mapping clarity.** "Stored" = the data files in S3. "Versioned" = the Iceberg snapshot chain that makes any historical state queryable. Lake Formation (or the S3 Tables built-in catalog) registers these as governed domain assets.

---

### 1B. Moved / Ingested → Kinesis + Glue ETL

**What it is.** The motion tier — data in transit from source to lake.

**AWS services.**

- **Amazon Kinesis Data Streams** — real-time ingestion at millisecond latency; shard-based throughput; enhanced fan-out gives dedicated 2 MB/s per consumer per shard. Use for clickstream, IoT telemetry, CDC event capture.
- **Amazon Data Firehose** (formerly Kinesis Data Firehose) — managed delivery from Kinesis/MSK/direct-put to S3, Redshift, or OpenSearch; supports Iceberg table delivery (writes directly to S3 Tables via Glue Data Catalog integration). Handles format conversion (JSON → Parquet), buffering, compression, and encryption in transit.
- **AWS Glue ETL (Spark-based)** — serverless Spark jobs for batch ingestion and transformation. Glue crawlers auto-discover schemas into the Glue Data Catalog. Glue supports Iceberg, Hudi, and Delta Lake as catalog-registered targets.
- **AWS Glue Streaming ETL** — continuous micro-batch from Kinesis or Kafka into S3/Iceberg; same job definition as batch but with a streaming context.

**Interview-ready facts.**

- Firehose → S3 Tables integration means you can stream directly into a managed Iceberg table without an ETL job for simple append workloads.
- Glue crawlers populate the Glue Data Catalog, which *is* the Lake Formation catalog — same metadata store, Lake Formation adds the authorization layer on top.
- For "Ingested" in DCO: the raw data lands in bronze/raw zone; it becomes a governed domain asset only after the catalog registers it and Lake Formation tags are applied.
- **Quality attribute — Timeliness/latency** (dominant on streaming leg): Kinesis delivers sub-minute freshness for use cases that genuinely require it.
- **Trade-off — Cost vs. freshness:** Kinesis continuous shard cost is only justified where a sub-minute SLA is real; batch (Glue ETL) everywhere else avoids paying for freshness nobody consumes.
- **Trade-off — Ingestion complexity vs. per-source correctness:** one unified streaming path is simpler to operate but forces batch-shaped sources into a streaming model they don't need.
- **Trade-off — Managed delivery vs. custom transform:** Firehose is zero-code append to S3/Iceberg; Glue Streaming ETL adds transformation-in-flight at the cost of job management overhead.

**DCO mapping clarity.** "Moved" = the pipeline runtime (Kinesis stream, Firehose delivery, Glue job). "Ingested" = the landing event that creates a new catalog entry. The River in the layer map.

---

### 1C. Modeled → Glue Jobs (Silver → Gold)

**What it is.** The transformation tier — curated, business-ready datasets derived from raw ingested data.

**AWS services.**

- **AWS Glue Jobs (PySpark / Spark SQL)** — the same Glue ETL service, but now running *transformation* logic: deduplication, joins, aggregation, SCD-2 merge, conforming to enterprise data models. Outputs write to silver (cleaned, conformed) and gold (business-aggregated, metric-ready) zones in S3/Iceberg.
- **AWS Glue Data Catalog** — central metastore for all databases, tables, and partitions. Stores schema, location, SerDe, table properties. Lake Formation wraps this with authorization; DataZone wraps it with domain-level discovery and governance.
- **AWS Glue DataBrew** — visual, no-code data preparation for analysts; 250+ built-in transformations. Not the primary modeling tool for an architect, but useful for data quality profiling and quick exploratory transforms.

**Interview-ready facts.**

- Glue 4.0+ uses Spark 3.3+; Glue now supports the open-source DeeQu framework for inline data quality checks within ETL jobs, not just at-rest catalog evaluation.
- Modeling in DCO terms is where schema-on-read (Iceberg raw) becomes schema-on-write (gold tables with enforced contracts). The Glue Data Catalog schema evolution features (add column, rename, type widening) support this without breaking downstream consumers when Iceberg is the table format.
- "Lake Formation catalog as the domain registry" — the catalog entry *is* the domain registration. No catalog entry = no governed domain asset. This is the gating mechanism.
- **Quality attribute — Data integrity & contract enforcement** (dominant): gold tables are schema-on-write; downstream consumers depend on enforced contracts, not hope.
- **Trade-off — Schema-on-read vs. schema-on-write:** flexibility at bronze (ingest everything, ask questions later) vs. correctness at gold (break the pipeline if the contract is violated).
- **Trade-off — Transformation latency vs. data freshness:** heavier modeling logic (SCD-2, identity resolution) delays gold availability; skip it and gold is fast but wrong.
- **Trade-off — Native (Glue) vs. open-source (dbt/Spark):** Glue is serverless and catalog-integrated; dbt/Spark is portable but requires separate orchestration and compute.

---

### 1D. Lake Formation Catalog — The Domain Registry

**What it is.** The metadata and authorization layer that turns raw S3 objects into governed data assets.

**Key capabilities.**

- **Glue Data Catalog** — unified metastore for all Hive-compatible, Iceberg, Hudi, Delta tables. Shared across Athena, EMR, Redshift Spectrum, Glue ETL, SageMaker.
- **Lake Formation registration** — registers S3 locations with the catalog; once registered, all access is mediated through Lake Formation permissions instead of raw S3/IAM policies.
- **LF-Tags (Tag-Based Access Control / TBAC)** — key-value tags (e.g., `sensitivity=confidential`, `domain=finance`) attached to databases, tables, or columns. Grants are made to tag expressions, not individual resources. New tables inheriting tags automatically inherit permissions — this is the scalability mechanism.
- **Cross-account sharing** — via AWS RAM. Version 3+ supports LF-Tag-based sharing at the Organizations level. Version 5 (Feb 2026) removes per-resource-type RAM limits via wildcard patterns.
- **SageMaker Lakehouse integration** — as of Aug 2025, TBAC extends to federated catalogs including S3 Tables, Redshift, DynamoDB, PostgreSQL, SQL Server.

**Interview-ready facts.**

- LF-Tags replaced the manual Named Resource method for large-scale governance; an architect should recommend TBAC for any deployment beyond ~50 tables.
- Lake Formation's two-layer permission model: IAM controls *who can call the API*, Lake Formation controls *what data they see*. Both must allow for access to succeed — this is a common interview trap.
- The catalog is the single point of truth for the "IS" pillar: if a dataset isn't registered here, it doesn't exist as a governed domain, regardless of whether the S3 objects physically exist.
- **Quality attribute — Security & discoverability** (dominant): the catalog is both the access-control enforcement point and the discovery surface for analysts.
- **Trade-off — Governance overhead vs. query latency:** every read pays an authorization hop through Lake Formation; loosening it cuts latency but reopens PII risk.
- **Trade-off — TBAC scalability vs. Named Resource precision:** LF-Tags scale to thousands of tables via inheritance but are coarser-grained than per-table grants; Named Resource is precise but unmanageable beyond ~50 tables.
- **Trade-off — Centralized catalog vs. federated autonomy:** single Glue Data Catalog simplifies governance but creates a control-plane dependency; DataZone adds domain-level delegation without fragmenting the catalog.

---

## Pillar 2: Constraints (Bound) — Policy and placement rules that govern data

### DCO Anchor

Two constraint families:
- **Policy tags: Govern · Validate · Trace** (GVT)
- **Placement tags: Topology · Resident · Isolated** (TRI)

---

### 2A. Govern → Lake Formation Fine-Grained Access + Tag-Based Policies

**What it is.** Authorization rules that control who can see what, at what granularity.

**Granularity levels (four tiers).**

1. **Table-level** — grant/revoke `SELECT`, `INSERT`, `DELETE`, `DESCRIBE` on entire tables.
2. **Column-level** — include-list or exclude-list specific columns from a grant (e.g., mask PII columns from analysts).
3. **Row-level** — data filters with SQL predicates (e.g., `region = 'APAC'`) that restrict which rows a principal can see.
4. **Cell-level** — combination of column + row filters; the most granular control.

**Tag-Based Access Control (TBAC / LF-Tags).**

- Tags are key-value pairs created in Lake Formation (e.g., `sensitivity: public | internal | confidential | restricted`; `domain: sales | marketing | finance`).
- Tags are assigned to catalog resources (databases, tables, columns).
- Permissions are granted to principals (IAM users, roles, SAML groups, IAM Identity Center groups) based on tag *expressions*, not individual resource names.
- Inheritance: new tables in a tagged database automatically inherit the database's tags and permissions — zero manual grant for new tables.
- As of Aug 2025, TBAC extends to federated catalogs (S3 Tables, Redshift, DynamoDB, PostgreSQL, SQL Server) via the SageMaker Lakehouse architecture.

**FGAC enforcement across engines.**

- Athena — full FGAC since GA.
- EMR on EC2 — full FGAC (EMR 6.x+).
- EMR on EKS — full FGAC (database, table, column, row, cell) with EMR 7.7+ (GA Feb 2025). Supports Iceberg, Hudi, Delta Lake.
- EMR Serverless — full FGAC supported.
- Redshift Spectrum — column/table-level; row-level via Redshift's own RLS.
- SageMaker Unified Studio — full FGAC with trusted identity propagation.

**Interview-ready facts.**

- Lake Formation FGAC does *not* enforce when data is accessed via direct S3 API calls (e.g., `s3:GetObject`) that bypass the catalog. This is by design — registration + credential vending is the enforcement boundary.
- Hybrid access mode lets you keep existing IAM/S3 policies while selectively opting tables into Lake Formation governance — important for migration stories.
- Cross-account FGAC: sharing via AWS RAM with LF-Tags works at the Organizations level. Version 5 (Feb 2026) removes per-resource-type RAM limits.
- **Quality attribute — Security & auditability** (dominant): every data access decision is authorized and logged; the constraint that makes compliance provable.
- **Trade-off — Enforcement strictness vs. query latency:** per-query authorization adds an overhead hop; relaxing it would speed queries but break the audit chain.
- **Trade-off — Granularity vs. administrative complexity:** cell-level FGAC is the most powerful control but exponentially increases the number of policies to author and test.
- **Trade-off — Centralized governance vs. team autonomy:** Lake Formation controls all access centrally; teams cannot self-serve data access without an admin grant (mitigated partly by TBAC inheritance).

---

### 2B. Validate → Glue Data Quality + Great Expectations

**What it is.** Rules that assert data meets defined quality contracts before it reaches downstream consumers.

**AWS Glue Data Quality.**

- Built on open-source DeeQu framework; serverless, no separate infrastructure.
- **DQDL (Data Quality Definition Language)** — domain-specific rule language. Rule types include: `IsComplete`, `IsUnique`, `ColumnValues` (range/set), `RowCount`, `DataFreshness`, `ReferentialIntegrity`, `CustomSql`, plus composite/NOT/WHERE rules.
- **Two evaluation modes:** (a) at-rest via Glue Data Catalog APIs (evaluate a cataloged table on a schedule), (b) in-transit within Glue ETL jobs (inline quality gate before writing to target).
- **ML anomaly detection** — Data Quality AI uses time-series forecasting to predict expected statistic ranges and flag anomalies without explicit thresholds. GA for Catalog-based evaluations as of Jul 2026.
- **Rule labeling** (GA Nov 2025) — attach key-value labels to rules (e.g., `criticality=high`, `team=finance`, `compliance=GDPR`). Labels flow into rule outcomes, enabling filtered reporting per team/domain/compliance requirement.
- **Preprocessing queries** (GA Nov 2025) — transform data before validation via SQL (derived columns, filters, calculations) without a separate ETL step.
- **Results storage in Glue Data Catalog** (GA Jul 2026) — rule outcomes, profiling metrics, anomaly predictions with confidence bounds written back to catalog tables, queryable via standard SQL.
- **Row-level results** — identify the exact records that failed quality checks; quarantine and fix at the record level.
- **EventBridge integration** — quality results published to EventBridge for alerting, downstream automation, incident creation.

**Great Expectations (open-source complement).**

- Python-based; defines "expectations" as JSON/YAML suites (similar role to DQDL rules but richer programmatic API).
- Runs inside Glue jobs, EMR Spark, or standalone. Useful when: (a) team already has GE suites, (b) need custom validation logic beyond DQDL, (c) want checkpoint-based validation with data docs (HTML reports).
- Not a replacement for Glue DQ — complementary. Glue DQ is native/serverless/integrated with catalog; GE is code-first/portable.

**Interview-ready facts.**

- **Quality attribute — Data integrity & trustworthiness** (dominant): the quality gate is what separates a data lake from a data swamp; without it, gold tables are unverified assertions.
- **Trade-off — Validation thoroughness vs. pipeline latency:** more DQDL rules per table = slower pipeline; prioritize by rule labels (`criticality=high`) to run expensive checks only where warranted.
- **Trade-off — Native (Glue DQ) vs. portable (Great Expectations):** Glue DQ is serverless and catalog-integrated; GE is code-first with richer programmatic API but requires separate compute and orchestration.
- **Trade-off — Explicit rules vs. ML anomaly detection:** deterministic DQDL rules are reproducible and auditable; ML anomaly detection catches unknown-unknowns but predictions carry confidence bounds, not guarantees.

**DCO mapping.** "Validate" = the quality gate. Data that fails validation stays in quarantine (bronze/rejected zone); data that passes moves to silver/gold. The DQDL ruleset *is* the validation constraint bound to the domain asset.

---

### 2C. Trace → CloudTrail + Lake Formation Audit Logs

**What it is.** The audit chain that proves who accessed what data, when, and what action was taken.

**AWS CloudTrail.**

- Records all API calls across AWS services as events. Two trail types: management events (control-plane: `CreateTable`, `GrantPermissions`) and data events (data-plane: `GetObject`, `PutObject` on S3).
- **CloudTrail Lake** — managed query engine for CloudTrail events; SQL-based analysis of audit logs without building a separate pipeline. Retains events for up to 7 years (configurable).
- S3 data events capture every read/write to lake objects — essential for demonstrating data lineage compliance (who read PII column X at timestamp T).

**Lake Formation audit logs.**

- Lake Formation logs all authorization decisions (allow/deny) to CloudTrail. Each entry shows: principal, resource (database/table/column), action attempted, LF-Tags evaluated, result.
- Combined with Glue Data Catalog events, this creates an end-to-end audit trail: registration → access grant → data access → quality check result.
- For compliance narratives (SOC2, HIPAA, PCI-DSS): the CloudTrail + Lake Formation audit chain is the primary evidence artifact.

**Interview-ready facts.**

- **Quality attribute — Auditability & compliance provability** (dominant): if you can't prove the Govern constraint was honored, the constraint doesn't exist for an auditor.
- **Trade-off — Trace granularity vs. storage cost:** S3 data events at scale generate massive log volume; enable only on buckets/prefixes that hold governed data, not utility buckets.
- **Trade-off — Real-time alerting vs. batch analysis:** EventBridge rules on CloudTrail give real-time breach alerts; CloudTrail Lake gives SQL-based forensic analysis — most architectures need both.
- **Trade-off — Retention period vs. cost:** CloudTrail Lake supports up to 7-year retention (compliance-friendly); self-managed archive to S3+Athena is cheaper for long tails but requires a pipeline.

**DCO mapping.** "Trace" = the tamper-proof record of every constraint enforcement decision. It answers: *was the Govern constraint honored? Was the Validate constraint executed? Can we prove it?*

---

### 2D. Topology / Resident / Isolated → Multi-Account via AWS Organizations

**What it is.** Placement constraints that dictate *where* data physically lives and which blast-radius boundaries apply.

**Topology — the account structure.**

- **AWS Organizations** — tree of OUs (Organizational Units) and accounts. Standard topology for a data platform: Shared Services OU (catalog, networking, CI/CD), Data Lake OU (raw/curated/consumption accounts), Analytics OU (Athena/EMR/Redshift workload accounts), Governance OU (audit, security, compliance accounts).
- **AWS Control Tower** — automated multi-account setup with guardrails (preventive via SCPs, detective via AWS Config rules). Landing Zone deploys the baseline OU/account structure.
- **Service Control Policies (SCPs)** — organization-wide permission boundaries. Example: SCP that prevents any account in the Data Lake OU from disabling CloudTrail or deleting Lake Formation tags.

**Resident — where data must stay.**

- S3 bucket region pinning — data sovereignty constraint. A bucket in `ap-south-1` (Mumbai) means data physically resides in India.
- S3 Object Lock (Governance Mode / Compliance Mode) — prevents deletion for a retention period; maps to regulatory hold requirements.
- VPC endpoints for S3 / Glue — traffic stays on AWS backbone, never traverses the public internet.

**Isolated — what must not cross boundaries.**

- Cross-account sharing via AWS RAM + Lake Formation controls which accounts can see which tagged datasets.
- Separate encryption keys per domain/account (KMS CMKs) — a dataset encrypted with the Finance KMS key cannot be decrypted by the Marketing account's role.
- Network isolation: VPC per workload account; PrivateLink for cross-account service access without peering.

**Interview-ready facts.**

- Multi-account is not optional at enterprise scale — it is the primary blast-radius and cost-attribution boundary. The Techwave JD's "scalable, reliable, future-ready" line implicitly requires a multi-account narrative.
- For the presales/RFP angle: a reference architecture for a regulated BFSI client starts with the OU topology, not the Glue job. The interviewer may test whether you lead with the infrastructure skeleton or jump straight to ETL.
- **Quality attribute — Blast-radius containment & data sovereignty** (dominant): separate accounts are the primary mechanism for limiting the damage radius of a misconfiguration or breach.
- **Trade-off — Isolation vs. collaboration:** separate accounts reduce risk but add cross-account sharing complexity (RAM invitations, LF-Tag grants, KMS key policies).
- **Trade-off — Control Tower guardrails vs. operational flexibility:** preventive SCPs block misuse but also block legitimate exceptions; detective guardrails flag without blocking but require response process.
- **Trade-off — Per-domain encryption (KMS CMKs) vs. key management overhead:** separate keys enforce cryptographic isolation but multiply key policies, rotation schedules, and cross-account grants.

---

## Pillar 3: Operations (Does) — What the platform does with governed, domain-registered data

### DCO Anchor

Four operation verbs: **Activate · Serve · Scale · Optimize** (ASSO).

---

### 3A. Activate / Serve → Athena + EMR Serverless

**What it is.** The compute tier that turns governed data into queryable insights and ML features.

**Amazon Athena.**

- Serverless, interactive SQL over S3-based data registered in the Glue Data Catalog. No infrastructure to manage; pay per TB scanned (or provisioned capacity for predictable workloads).
- Supports Iceberg, Hudi, Delta Lake, Parquet, ORC, CSV, JSON, Avro.
- **Athena Federated Query** — Lambda-based connectors to query DynamoDB, RDS, Redshift, CloudWatch Logs, on-prem JDBC sources *from the same SQL statement* as S3 data.
- **Athena for Spark** — run PySpark notebooks directly in Athena; serverless Spark sessions with no EMR cluster.
- FGAC via Lake Formation is fully enforced — column masking, row filtering at query time.

**Amazon EMR Serverless.**

- Fully managed Spark/Hive/Tez execution; no cluster provisioning, no instance type selection.
- Auto-scales compute dynamically based on workload — ramps up workers for heavy jobs, releases immediately when idle.
- As of Dec 2025: serverless storage decouples shuffle data from compute — eliminates disk capacity failures and reduces cost by up to 20%.
- Multi-AZ resilience by default; bills per second for vCPU + memory consumed.
- Full FGAC via Lake Formation for Spark batch jobs.
- Latest release: EMR 7.9.0 (Jul 2025) — Spark 3.5.5, Hive 3.1.3, Tez 0.10.2.
- Available in expanding regions including Asia Pacific (Hyderabad) as of May 2026.

**Interview-ready facts.**

- **Quality attribute — Cost-efficiency per query profile** (dominant): the right engine per workload shape is the single largest cost lever on the Operations layer.
- **Trade-off — Fixed vs. per-query cost:** EMR (managed cluster) amortizes for sustained heavy joins; Athena per-scan pricing wins for spiky, unpredictable analyst queries where idle cost should be zero.
- **Trade-off — Operational simplicity (one engine) vs. cost-fit (right engine per workload):** forcing one engine pays the wrong cost curve on half the workload; two engines double the monitoring surface.
- **Trade-off — Serverless cold-start latency vs. always-on responsiveness:** EMR Serverless and Athena have startup overhead; pre-warmed EMR clusters respond instantly but cost money when idle.

**DCO mapping.** "Activate" = run a query or model training job that reads governed data and produces output. "Serve" = make the result available to a consumer (dashboard, API, application). Athena activates for ad-hoc/interactive; EMR Serverless activates for batch/heavy-compute. Both read from the same catalog, same Lake Formation permissions.

---

### 3B. Serve (BI workloads) → Redshift Serverless

**What it is.** Managed data warehouse for structured BI workloads with sub-second query performance on large datasets.

**Amazon Redshift Serverless.**

- No cluster sizing; pay per RPU-hour consumed. Scales up/down automatically within configured min/max RPU bounds.
- **Redshift Spectrum** — queries S3/Iceberg data directly using the Glue Data Catalog, without loading into Redshift storage. Joins external S3 data with local Redshift tables in the same query.
- **Materialized views over external tables** — pre-compute aggregations on lake data for BI dashboards without full data movement.
- **Redshift ML** — `CREATE MODEL` SQL command invokes SageMaker Autopilot; inference via SQL `SELECT` — no separate ML infrastructure visible to the analyst.
- **Zero-ETL integrations** — Aurora, DynamoDB, and RDS data replicated into Redshift automatically; no Glue job needed for these specific sources.

**When Redshift vs. Athena.**

- Athena: ad-hoc, exploratory, infrequent, schema-on-read, federated queries across diverse sources. Cost model: per-scan.
- Redshift Serverless: repeated BI dashboards, complex joins/aggregations, concurrent users, SLA-bound query latency. Cost model: per-compute-time.
- Both read the same Glue Data Catalog. Lake Formation governs both. They are not competing services — they serve different query profiles in the same lake architecture.

**Interview-ready facts.**

- **Quality attribute — Query concurrency & dashboard SLA** (dominant): Redshift is chosen when concurrent BI users need sub-second latency on repeated queries, not for one-off exploration.
- **Trade-off — Data movement (load into Redshift) vs. in-place query (Spectrum on S3):** local Redshift storage gives fastest latency; Spectrum avoids duplication but scans S3 at query time.
- **Trade-off — Zero-ETL convenience vs. Glue ETL control:** auto-replication from Aurora/DynamoDB is simpler but offers no transformation; Glue ETL adds cost but enables silver→gold modeling before Redshift.
- **Trade-off — Redshift Serverless RPU cost vs. Athena per-scan cost:** depends on query repetition and concurrency; Redshift wins at high concurrency, Athena wins for infrequent exploratory queries.

---

### 3C. Scale → Auto-Scaling Patterns Across Glue / EMR / Redshift

**What it is.** Elasticity rules that match compute capacity to workload demand.

**Glue scaling.**

- Glue ETL jobs: specify max DPUs (Data Processing Units); Glue auto-allocates workers within that cap. Auto Scaling (GA) dynamically adds/removes workers during job execution based on shuffle/spill metrics.
- Glue Data Quality evaluations scale with the underlying table size; serverless, no tuning.

**EMR Serverless scaling.**

- Fully automatic: submit job → EMR provisions workers → scales up during heavy stages → releases workers when idle → shuts down after job completes. No auto-scaling rules to configure.
- You set max vCPU/memory/disk limits per application to bound spend, not to tune performance.

**EMR on EC2 scaling (if the interviewer asks about managed clusters).**

- Managed Scaling: EMR monitors YARN metrics and adds/removes core/task nodes within defined min/max bounds.
- Instance Fleets: mix On-Demand + Spot across multiple instance types; EMR selects the cheapest available combination.

**Redshift Serverless scaling.**

- RPU (Redshift Processing Units): set base and max RPUs. Redshift Serverless automatically adjusts within this range per query concurrency and complexity.
- Concurrency scaling: automatically adds transient clusters for burst read queries; first hour per day is free.

**Interview-ready facts.**

- **Quality attribute — Elasticity & cost containment** (dominant): scaling is not about going big — it's about matching capacity to demand without overspend or underserve.
- **Trade-off — Auto-scale headroom vs. cost:** higher max caps enable burst handling but risk runaway spend if a bad query or loop triggers unbounded scaling.
- **Trade-off — Serverless (EMR Serverless, Athena) vs. managed (EMR on EC2):** serverless is zero-ops but you trade away tuning control; managed clusters offer knob-level optimization but require staffing.
- **Trade-off — Spot instance savings vs. job reliability:** EMR Instance Fleets save up to 60–90% on task nodes but Spot interruptions can fail long-running jobs; mitigate with checkpointing and core-on-demand.

**DCO mapping.** "Scale" is the elasticity constraint on operations. Every Activate/Serve engine has a knob: DPU cap (Glue), vCPU/memory cap (EMR Serverless), RPU range (Redshift Serverless). The architect's job is setting the bound, not tuning the internals.

---

### 3D. Optimize → Cost Attribution via CUR + Resource Tags

**What it is.** The feedback loop that measures what the platform costs and attributes spend to the right owner.

**AWS Cost and Usage Report (CUR / CUR 2.0).**

- Most granular billing dataset AWS produces: line-item detail per service, per resource ID, per hour (or day/month), per usage type.
- Delivered as compressed CSV or Parquet to S3; queryable via Athena + Glue Data Catalog (same catalog that governs the lake — this is architecturally elegant).
- CUR includes: unblended/blended/amortized costs, reservation and Savings Plans attributes, resource IDs, cost allocation tags.
- CUR 2.0 (Data Exports) adds `line_item_iam_principal` for per-role cost attribution (e.g., per-application Bedrock spend).

**Cost allocation tags.**

- Two types: AWS-generated (e.g., `aws:createdBy`) and user-defined (e.g., `team`, `project`, `environment`, `cost-center`).
- Tags must be *activated* in the Billing console to appear in CUR and Cost Explorer; activation is a separate step from tagging the resource.
- As of Dec 2025: account-level cost allocation tags via AWS Organizations — attribute even untaggable resources (refunds, credits, support fees) to the correct account grouping.
- Not every resource supports resource-level tags. S3 bucket tags appear in billing; object-level tags do not. ECS requires explicit tag propagation.

**Cost optimization levers (architect-level).**

- Athena: partition pruning + columnar format (Parquet/Iceberg) reduces TB scanned → reduces cost.
- Glue: right-size DPU allocation; use Glue Auto Scaling to avoid over-provisioning.
- EMR Serverless: inherently pay-per-use; optimize by tuning Spark configs (shuffle partitions, broadcast thresholds) to reduce total vCPU-seconds.
- Redshift Serverless: right-size RPU range; use materialized views to avoid re-scanning S3.
- S3: lifecycle rules (Intelligent-Tiering, Glacier) for cold data; S3 Tables auto-compaction reduces file count → reduces Athena scan cost.

**Interview-ready facts.**

- **Quality attribute — Financial accountability & waste elimination** (dominant): if you can't attribute cost to an owner, nobody optimizes; CUR + tags make cost visible and actionable.
- **Trade-off — Tagging discipline vs. operational burden:** comprehensive tags require enforcement (Tag Policies in Organizations); gaps create unattributed spend that no dashboard can fix.
- **Trade-off — CUR granularity (hourly) vs. analysis cost:** finer-grained CUR means larger Parquet datasets in S3 to query; partition by month and use Athena to keep analysis cost proportional.
- **Trade-off — Savings Plans commitment vs. flexibility:** reserved capacity (Compute Savings Plans, Redshift Reserved Nodes) saves 30–60% but locks you in; over-commit and you pay for capacity you've outgrown.

**DCO mapping.** "Optimize" = the cost and performance feedback loop. CUR + tags give you the measurement; the architect translates measurement into action (resize, re-partition, archive, kill idle). Without this loop, Scale is just spending money faster.

---

## Cross-Pillar Interview Pattern

**Start:** Business outcome (Layer 1) — "This architecture enables the data team to answer X question with Y latency at Z cost."
**Descend only when pulled:** DCO mechanics — "The Iceberg snapshot chain gives us time-travel for regulatory lookback; Lake Formation TBAC scales governance to 500+ tables without per-table grants; CUR + Athena gives us per-team cost attribution without a separate FinOps tool."
**Return:** Business frame — "Net effect: the platform is auditable for SOC2, self-service for analysts, and cost-transparent for finance."

**Role boundary reminder:** Your verb is *verify evidence for someone else's sign-off*. You review the architecture, run the trade-off analysis, surface risks, recommend — you do not own the gate. "I verified that the partition strategy met the latency SLA before the lead architect signed off" is credible. "I signed off the architecture gate" is an overclaim at Director level.

---
---

# Part 2 — AWS Lakehouse Architect Reference (Design · Trace · STAR · Mnemonics)

---

## AWS layer-map: 
- Lake Formation→Gate/govern, S3+Iceberg→Lake/stored-versioned, Kinesis+Glue ETL/Glue Jobs→River/moved-ingested+modeled, Athena+EMR→Engine/activates. All four layers and their tag pairings match

Inline, formatted with markdown — not an actual file, consistent with this whole session's pattern.

**Either is enough — they're not both required.** You pick per workload; nothing forces you to run both.

| | Athena | EMR |
|---|---|---|
| **Type** | Serverless query engine (pay-per-query) | Managed cluster (you provision/size Spark, Hive, etc.) |
| **When used** | Ad-hoc SQL, interactive analytics, low/spiky usage | Heavy, sustained, custom, or non-SQL processing (large ETL, ML, streaming) |
| **Activates architecture alone?** | Yes — sufficient if workload is SQL-query-shaped | Yes — sufficient if workload is compute-heavy/custom |
| **Typical combo** | Often used *together* in practice: EMR does heavy transform (Silver→Gold), Athena serves ad-hoc queries after — not required, just common |

**One-line for recall:** *"Athena is the tap you turn on per query; EMR is the engine room you keep running."* Either alone activates the architecture — using both together is a common pattern, not a structural requirement.

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

### Layer vs. Config — AWS · Databricks · Snowflake

| Layer | AWS — config | Databricks — config | Snowflake — config |
|---|---|---|---|
| **Gate** (Govern) | LF-Tags (key-value, tag expressions) + FGAC grants (table/column/row/cell) | Unity Catalog grants + dynamic views (masks, row-filters via SQL functions) | Masking policies (column) + row-access policies (SQL predicate) |
| **Lake** (Stored/Versioned) | Iceberg snapshot metadata (manifest lists) + S3 versioning/lifecycle rules | Delta transaction log (`_delta_log`) + `VACUUM`/`RESTORE` for Time Travel | Time Travel (`AT`/`BEFORE` clause, up to 90 days) + Fail-safe (7-day recovery, Snowflake-managed) |
| **River** (Moved/Ingested + Modeled) | Kinesis shard count/fan-out + Glue DPU cap + Auto Scaling | Auto Loader (`cloudFiles` schema inference) + DLT/Lakeflow pipeline config (expectations, triggers) | Snowpipe (auto-ingest via cloud notification) + Dynamic Tables (`TARGET_LAG` refresh config) |
| **Engine** (Activates) | Athena workgroup (bytes-scanned limit) + EMR Serverless (vCPU/memory/disk cap) | Photon toggle on cluster config + cluster autoscaling (min/max workers) | Virtual Warehouse size (XS–6XL) + multi-cluster auto-suspend/resume |

---

## Rung 6.5 — Layer → Service → Config → Purpose → Applicability (Master Table)

The gap this closes: naming a service ("Athena") when the interviewer wants the config knob ("Athena workgroup with bytes-scanned limit + partition by date"). This table pairs every layer's primary services with their production-relevant configs, the *purpose* of each config, and *when* to reach for it.

### AWS

| Layer | Service | Config | Purpose | Applicability |
|---|---|---|---|---|
| **Gate** | Lake Formation | **LF-Tags** (key-value tag expressions) | Scale governance via tag inheritance; new tables inherit permissions automatically | >50 tables; enterprise-wide PII/classification tagging |
| **Gate** | Lake Formation | **Column-level FGAC** | Mask PII columns from analyst roles | Any table with mixed-sensitivity columns |
| **Gate** | Lake Formation | **Row-level filter** (SQL predicate) | Multi-tenant isolation; region-based data restriction | Multi-tenant lakes; GDPR/geo-residency constraints |
| **Lake** | S3 + Iceberg | **Snapshot expiration policy** | Prevent metadata bloat from unbounded snapshot history | Any Iceberg table in production; especially high-write frequency |
| **Lake** | S3 + Iceberg | **Compaction thresholds** (target file size) | Merge small files → reduce query scan overhead | Streaming writes producing many small files; frequent gold-layer writes |
| **Lake** | S3 + Iceberg | **Partition evolution** | Change partitioning strategy without rewriting historical data | Schema changes over time; late-arriving-data patterns |
| **River** | Kinesis Data Streams | **Shard count** (throughput-sized) | Match ingest capacity to source event rate (1 shard = 1MB/s in) | Sized by peak events/sec ÷ 1000; 2M/day BFSI ≈ 3–5 shards |
| **River** | Kinesis Data Streams | **Enhanced fan-out** (2MB/s per consumer) | Dedicated throughput per consumer, no shared-shard contention | 3+ consumers on same stream (fraud model + audit + analytics) |
| **River** | Kinesis Data Streams | **Retention window** (24hr–365 days) | Enable replay for reprocessing, late consumers, DR | Any stream with downstream idempotent consumers or DR RPO need |
| **River** | Glue ETL (Spark) | **DPU cap + Auto Scaling** | Bound cost while allowing burst; auto-adjust workers per stage | All batch ETL; especially variable-workload jobs (nightly batch) |
| **Engine** | Athena | **Workgroup** (bytes-scanned limit + query timeout) | Cost governance per team/workload; prevent runaway queries | Multi-team environments; ad-hoc analyst access |
| **Engine** | Athena | **Partition-by-date + Iceberg pruning** | Reduce data scanned per query → 90%+ cost cut on time-range queries | Any time-series data (transactions, events, logs); 90-day audit queries |
| **Engine** | EMR Serverless | **Application config: vCPU/memory/disk cap** | Bound spend; avoid runaway serverless spin-up | Sustained heavy Spark batch (40M+ records) |
| **Engine** | EMR Serverless | **Spark shuffle partitions** | Match parallelism to data size; fix skew symptoms | Skewed joins; last-2%-slow symptoms; wide aggregations |
| **Engine** | Redshift Serverless | **RPU min/max cap** | Bound concurrency-driven spend | Predictable BI dashboards; 15+ concurrent analysts |

### Databricks (cross-platform pivot)

| Layer | Service | Config | Purpose | Applicability |
|---|---|---|---|---|
| **Gate** | Unity Catalog | **Dynamic views** (SQL mask functions) | Column-level masking without duplicating tables | PII columns; role-based reveal |
| **Lake** | Delta Lake | **`VACUUM` retention + `OPTIMIZE`** | Reclaim storage; compact small files | High-write tables; streaming sinks |
| **River** | Auto Loader | **`cloudFiles` schema inference** | Auto-detect new files; handle schema drift | Landing zone with unpredictable schema evolution |
| **River** | DLT/Lakeflow | **Expectations + triggers** | Declarative data quality + pipeline mode (continuous vs. triggered) | Regulated pipelines needing built-in DQ enforcement |
| **Engine** | Photon | **Toggle on cluster config** | Vectorized C++ execution for 3–8× faster SQL | Heavy SQL analytics; cost-per-query optimization |

### Snowflake (cross-platform pivot)

| Layer | Service | Config | Purpose | Applicability |
|---|---|---|---|---|
| **Gate** | Masking policies | **Column-level SQL function** | Runtime PII masking by role | Any PII column; policy attached at column DDL |
| **Lake** | Time Travel | **`AT`/`BEFORE` clause + retention (up to 90 days)** | Query historical state; recover accidentally dropped rows | Any table needing audit lookback within 90 days |
| **River** | Snowpipe | **Cloud notification trigger** | Near-real-time file ingest (seconds latency) | Continuous file drops from external sources |
| **River** | Dynamic Tables | **`TARGET_LAG` refresh config** | Declarative freshness SLA; Snowflake picks refresh cadence | Silver→Gold transformations with defined freshness target |
| **Engine** | Virtual Warehouses | **Size (XS–6XL) + multi-cluster auto-suspend/resume** | Elastic compute per workload; zero cost when idle | Spiky BI workloads; per-team warehouse isolation |

---

**How to use this table in the 10-move drill:**
- **Move 2 (Service)** fires the service name.
- **Move 5 (Config)** fires the config parameter — this is where you were slipping.
- **Applicability column** is the "why this config *for this scenario*" — it prevents rote answers by forcing you to justify the config against the scenario's actual volume/latency/tenancy signals.

---

### Daily drill order (five clauses, one breath each)

**Axes → Tags → Layers → AWS anchors → Engine-choice line.**

Add the trade-off pairs only on days you have time for the sixth clause; the first five are the non-negotiable daily rep.

---

## Rung 7 — Observability/Proof: Correctness + Performance, per layer, consolidated

Observability is both: **correctness** (is the config doing what it's supposed to?) and **performance** (is it doing it efficiently?). Both questions need a gauge. AWS observability is inherent per layer, each tied to a native artifact — consolidated via **CloudWatch** (metrics/logs/alarms), **CloudTrail Lake** (SQL-queryable audit across all services), and **Amazon DataZone** (lineage, quality, and access audit in one governance plane).

**Across all layers:**

| Layer | Correctness gauge | Performance gauge |
|---|---|---|
| **Gate** | LF-Tag audit log — was PII masked on every query? | IAM/LF permission resolution latency |
| **Lake** | Iceberg snapshot count — are old snapshots expiring per lifecycle rule? | Compaction metrics — file count before/after |
| **River** | Glue job bookmark — did it pick up exactly where it stopped? | DPU utilization vs. cap, shuffle spill |
| **Engine** | Athena data-scanned per query — partition pruning working? | Query execution time, EMR vCPU saturation |

**Beyond layers — other observability surfaces:**
- Data quality: Glue DQ rule pass/fail rates, row-level rejection counts
- Cost: CUR anomaly alerts, per-tag spend drift
- Lineage: AWS Glue lineage graph, Unity Catalog lineage, OpenLineage events

**Tooling:**

| Scope | Tool |
|---|---|
| Unified metrics/logs/alarms | Amazon CloudWatch |
| Cross-service audit (SQL) | AWS CloudTrail Lake |
| Data governance plane | Amazon DataZone |
| Glue DQ alerting | EventBridge |
| Cross-platform lineage | OpenLineage / Marquez |
| Databricks | Lakehouse Monitoring |
| Snowflake | `QUERY_HISTORY` + `METERING_HISTORY` |

Config is the intent. Observability artifacts are the evidence. Tools are how you read them.

---

## Compression Mnemonic — SILC · MCC · CBC (Twist Follow-up Clusters)

After the 3-tier answer lands, RK's next move is usually a *twist follow-up* — an unscripted probe into a specific failure mode, operational reality, or soft-skill area. These fall into three named clusters. When you hear the trigger phrase, name the cluster mentally, then pick the matching candidate.

| Cluster | Full form | Gaps covered | RK trigger phrase |
|---|---|---|---|
| **SILC** | Pipeline correctness | **S**chema evolution · **I**dempotency · **L**ate-arriving data · **C**DC (Change Data Capture) | *"How does your pipeline handle X data condition?"* |
| **MCC** | Operational reality | **M**ulti-tenancy isolation · **C**ost spike diagnosis · **C**atalog federation | *"How does this behave in production / at scale?"* |
| **CBC** | Soft-skill probes | **C**ritique-this-design · **B**ehavioral pushback · **C**onvergence questions (AWS vs. Databricks vs. Snowflake) | *"Tell me about a time..."* or *"What would you change?"* |

**Reflex pattern:** trigger phrase heard → cluster identified → 3–4 candidates in mind → pick the matching one. Same attribute-first shape as Rungs 4–6, one abstraction layer up.

**Gap descriptions (one line each):**

| Cluster | Gap | One-line answer shape |
|---|---|---|
| SILC | Schema evolution | Iceberg partition/schema evolution handles add/rename/drop without breaking downstream; raw Parquet does not. |
| SILC | Idempotency | Idempotency key on the sink write + dedup at merge; Kinesis shard replay is safe. |
| SILC | Late-arriving data | Iceberg partition evolution + watermarking; late event lands in correct partition without gold corruption. |
| SILC | CDC | RDS/Aurora → Kinesis → Iceberg MERGE; upsert pattern with change-record ordering. |
| MCC | Multi-tenancy | Separate S3 prefixes + Lake Formation account-level isolation + per-tenant KMS CMK. |
| MCC | Cost spike diagnosis | CUR + per-tag spend drift + CloudTrail Lake query for the offending principal/service. |
| MCC | Catalog federation | Unity Catalog ↔ Lake Formation federation (metadata reference, not copy); avoids sync drift. |
| CBC | Critique-this-design | Run the attribute-first reflex on the shown design; name what QA it under-optimizes. |
| CBC | Behavioral pushback | STAR: situation → complication → resolution; role-boundary intact (verify, not sign off). |
| CBC | Convergence questions | Cross-platform pivot table (AWS / Databricks / Snowflake config columns) applied to the same layer. |

**Highest-probability gaps for BFSI + Techwave context:** Idempotency, Late-arriving data, Multi-tenancy, CDC, Behavioral pushback. Drill these first.

---

## Concept Cross-Cuts — Three Rungs the Ladder Doesn't Explicitly Cover

The 12-rung ladder covers **where data lives** (Rungs 1–12) and **how you deliver it** (3-tier flow + SILC/MCC/CBC). Three architecture concepts sit *across* the ladder rather than on it. RK may probe any of these mid-scenario — surface each in one clean sentence when triggered.

### Cross-Cut A — Data Modeling (how data is *shaped*)

| Model | When to use | One-line answer |
|---|---|---|
| **Dimensional (Kimball)** — star/snowflake schema | BI dashboards, aggregation-heavy gold layer | Facts (metrics) + dimensions (context); denormalized for read performance. |
| **Data Vault 2.0** — hubs, links, satellites | Regulated domains needing full audit lineage + change history | Hubs (business keys) + Links (relationships) + Satellites (attributes + change tracking). |
| **Normalized (3NF)** — source-system-like | Silver layer, integration zones, source-mirror | Referential integrity preserved; minimal redundancy; not query-optimized. |

**Trigger:** RK asks *"how would you model this for the gold layer?"* → answer: dimensional for BI, vault for regulated audit, normalized for silver/integration.

---

### Cross-Cut B — Integration Patterns (runtime system-to-system)

| Pattern | Purpose | AWS anchor |
|---|---|---|
| **API contracts** (OpenAPI/AsyncAPI) | Producer-consumer coupling boundary | API Gateway + schema registry |
| **Event schemas** (Avro/Protobuf) | Schema evolution in event-driven systems | AWS Glue Schema Registry (Avro/JSON/Protobuf) |
| **CDC replay semantics** | Idempotent re-ingestion after failure | Kinesis + Iceberg MERGE with dedup key |

**Trigger:** RK asks *"how do you handle contract changes between systems?"* → answer: schema registry with backward-compatibility rules; CDC replay uses idempotency key at sink.

---

### Cross-Cut C — Non-Functional Requirements (NFRs)

| NFR | Definition | Design signal |
|---|---|---|
| **SLA** (Service Level Agreement) | Business commitment (e.g., 99.9% availability, sub-second query latency) | Drives engine choice (Redshift vs. Athena) and multi-AZ topology |
| **RTO** (Recovery Time Objective) | Max acceptable downtime after failure | Drives DR posture — multi-region active-active vs. warm standby |
| **RPO** (Recovery Point Objective) | Max acceptable data loss (in time) | Drives backup frequency, snapshot cadence, cross-region replication interval |
| **Data classification tiers** (Public / Internal / Confidential / Restricted) | Sensitivity level dictating governance controls | Drives LF-Tag assignment, KMS key selection, audit retention period |

**Trigger:** RK asks *"what's your RPO?"* or *"what classification does this data carry?"* → NFRs are first-class inputs to layer design, not afterthoughts.

---

**How to use these cross-cuts in the 10-move drill:** they don't add a new move — they surface *inside* Move 4 (Trade-off) or Move 6 (Artifact) when the scenario mentions modeling, integration, or NFRs explicitly. If RK says "regulated audit trail," you name Data Vault; if he says "sub-second SLA," you name RTO/engine implication.

---

*Verification note: AWS service capabilities (Lake Formation ABAC scope, Iceberg-on-Glue feature parity with Delta, Athena engine v3 changes) evolve — verify against current AWS docs before this becomes a build reference.*