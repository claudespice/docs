---
description: Frequently asked questions
icon: circle-question
---

# FAQ

### What's the difference between the Spice.ai Cloud Platform and Spice.ai OSS?

[**Spice.ai OSS**](https://github.com/spiceai/spiceai) is an open-source project created by the Spice AI team that provides a unified SQL query interface to locally materialize, accelerate, and query data tables sourced from any database, data warehouse, or data lake.

<figure><img src="../.gitbook/assets/image (41).png" alt=""><figcaption><p>The Spice.ai OSS runtime</p></figcaption></figure>

**The Spice.ai Cloud Platform** is a data and AI application platform that provides a set of building-blocks to create AI and agentic applications. Building blocks include a cloud-data-warehouse, ML model training and inference, and a cloud-scale, managed Spice.ai OSS cloud-hosted service.

<figure><img src="../.gitbook/assets/OGP (1).png" alt=""><figcaption><p>The Spice.ai Cloud Platform</p></figcaption></figure>

### How much does Spice.ai Cloud cost?

It's free to [get an API key](https://spice.ai) to use the [Community Edition](/broken/pages/TswKQuxvMpeSWfvqFhxa).

Customers who need resource limits, service-level guarantees, or priority support we offer [high-value paid tiers](../pricing/plans.md) based on usage.

### What level of support do you offer?

We offer enterprise-grade support with an SLA [for Enterprise Plans](../pricing/plans.md).

For standard plans we offer [best-effort community support](https://github.com/spicehq/cloud-docs/blob/trunk/broken-reference/README.md) in Discord.

### What's your approach to security and compliance?

See [Security](../security/security.md). The Spice.ai Cloud Platform is SOC 2 Type II compliant.

### What SQL query engine/dialect do you support?

Spice.ai OSS is built on [Apache DataFusion](https://datafusion.apache.org/) as its primary query execution engine, providing vectorized, multi-threaded query processing. It uses the PostgreSQL SQL dialect. Spice also supports [DuckDB](https://duckdb.org/), SQLite, and PostgreSQL as acceleration engines at the dataset level.

### What AI capabilities does Spice provide?

Spice provides unified APIs for data and AI workflows, including model inference, embeddings, and an [AI gateway](../features/ai-gateway.md) supporting OpenAI, Anthropic, Amazon Bedrock, and xAI. Spice also includes advanced tools such as [vector and hybrid search](../features/search-and-retrieval.md), text-to-SQL, and data sampling.

### What AI model providers does Spice support?

Spice supports local model serving (e.g. Llama) and gateways to hosted AI platforms including OpenAI, Anthropic, xAI, and Amazon Bedrock. See [Model Providers](../building-blocks/model-providers/) for details.

### Can Spice handle federated queries?

Yes. Spice natively supports [federated SQL queries](../features/federated-sql-query.md) across disparate data sources with advanced query push-down capabilities, executing portions of queries directly on source databases to reduce data transfer and improve performance.

### Can Spice integrate with existing BI tools?

Yes. Spice integrates with BI tools through standard SQL interfaces (ODBC, JDBC, ADBC, Arrow Flight SQL), enabling accelerated, real-time analytics for dashboards and reporting.

### Does Spice support Change Data Capture (CDC)?

Yes. Spice supports CDC via [Debezium](../building-blocks/data-connectors/debezium.md), enabling real-time data ingestion and materialization from databases such as PostgreSQL and MySQL.

### How do I keep an accelerated dataset incrementally up-to-date?

For sources that expose a monotonically-increasing version column (e.g. `updated_at`, `lastUpdateTime`), Spice can incrementally ingest only new or modified records using `time_column` together with `refresh_mode: append` and a `refresh_check_interval`. Combined with `retention_period`, old records are automatically evicted so the accelerated replica stays bounded in size.

Behavior:

- **Initial load**: Spice loads all records from the source where `time_column > now() - refresh_data_window`.
- **Incremental refresh**: On each `refresh_check_interval`, Spice queries the source for records where `time_column` is newer than the most recent value already in the accelerated store, and appends them. If `primary_key` is set, matching rows are upserted instead of duplicated.
- **Overlap window**: Use `refresh_append_overlap` to widen the incremental query to `time_column > max(time_column) - refresh_append_overlap`. This re-reads a small trailing window on every refresh to tolerate clock skew between the source and the runtime, and to pick up late-arriving writes whose `time_column` is slightly behind the refresh boundary. Combined with `primary_key` upserts, any rows re-read in the overlap are deduplicated rather than duplicated — so no records are lost near the refresh boundary and no duplicates are introduced.
- **Retention**: On each `retention_check_interval`, rows where `time_column` is older than `retention_period` are removed from the accelerated store, bounding storage and aging out data that is no longer needed.

**Handling deletes.** For sources that do not emit a change feed (e.g. HTTP APIs), the recommended pattern is **soft deletes**: the source marks removed records with a `deleted` flag (and bumps `time_column`). The incremental refresh picks up the tombstone via the normal append path, the upsert replaces the live row with its soft-deleted version, and `retention_period` eventually evicts it from the accelerated store. Queries should filter `WHERE deleted = false` (or use a [view](../building-blocks/views/)) to hide soft-deleted rows. This avoids the cost of periodic full snapshots.

If soft deletes are not available, schedule a periodic `refresh_mode: full` snapshot to reconcile hard deletes by atomically replacing the accelerated contents. For sources that emit a complete change feed (e.g. Debezium, Kafka), use [`refresh_mode: changes`](../building-blocks/data-connectors/debezium.md) instead to propagate inserts, updates, and deletes in real time.

Example — keep the last 90 days of GitHub pull requests, checking for new/updated records every 15 minutes, with a 5-minute overlap to cover clock skew and late arrivals:

```yaml
datasets:
  - from: github:github.com/spiceai/spiceai/pulls
    name: pulls
    params:
      github_token: ${secrets:GITHUB_TOKEN}
      github_query_mode: search
    time_column: updated_at
    acceleration:
      enabled: true
      refresh_mode: append
      refresh_check_interval: 15m
      refresh_append_overlap: 5m
      refresh_data_window: 90d
      primary_key: id
      retention_period: 90d
      retention_check_interval: 1h
```

### Does Spice support schema evolution?

Spice infers the schema for datasets and views at startup and does not apply runtime schema changes by default. If the source schema changes while the runtime is running (e.g. columns are added, removed, or their types change), data refreshes will fail with a schema mismatch error rather than silently applying the new schema.

To pick up a new source schema, restart the Spice runtime. On startup, Spice re-infers the schema from the source and the accelerated table is re-initialized with the updated schema.

### What is Data-grounded AI?

Data-grounded AI anchors models in accurate, current, domain-specific data rather than relying solely on pre-trained knowledge. Spice unifies enterprise data across databases, data lakes, and APIs, dynamically incorporating real-world context at inference time. This helps minimize hallucinations, reduce operational risk, and build trust in AI by delivering reliable, relevant outputs.

### Where can I find examples and recipes?

The [Spice.ai Cookbook](https://github.com/spiceai/cookbook) provides quickstarts and examples demonstrating Spice capabilities, including federated queries, RAG, text-to-SQL, and more.
