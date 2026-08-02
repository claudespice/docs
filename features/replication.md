---
icon: arrows-rotate
description: Continuously replicate committed changes from operational databases into an accelerated, query-ready replica
---

# Replication

**Replication** keeps an accelerated dataset continuously in step with its source by reading the source database's own changelog. Committed inserts, updates, and deletes are applied to the local replica within seconds, with no batch window and no external pipeline.

Replication is enabled by setting `refresh_mode: changes` on an accelerated dataset. Spice reads the source's native change feed — the PostgreSQL write-ahead log, a MongoDB change stream, a DynamoDB stream — and applies each change to the accelerator as it commits.

{% hint style="info" %}
Replication is one of three [refresh modes](data-acceleration/README.md#refresh-modes). Use `full` to replace a dataset on each refresh, `append` for immutable or time-series data, and `changes` to mirror a mutable source that emits a change feed.
{% endhint %}

### Why replicate

Running analytical queries against a production database competes with transaction processing for the same connections, buffer pool, and CPU. The usual alternatives each carry a cost:

* **ETL pipelines** add latency measured in minutes or hours, plus the infrastructure to build, schedule, and monitor them.
* **Read replicas** relieve the primary but run the same row-oriented engine, so analytical scans remain slow.
* **HTAP databases** require migrating off the existing system and couple transactional and analytical failure domains.

Replication into a columnar accelerator avoids all three. The operational database keeps serving transactions, analytical load lands on separate storage and compute, and the replica stays seconds behind rather than hours.

For the architecture built on this capability, see [Analytics Replica](../use-cases/analytics-replica.md).

### Supported sources

| Source | Mechanism | Configuration |
| ------ | --------- | ------------- |
| [PostgreSQL](../building-blocks/data-connectors/postgres.md) | Logical replication from the write-ahead log | `refresh_mode: changes` |
| [MongoDB](../building-blocks/data-connectors/mongodb.md) | Change streams on the source collection | `refresh_mode: changes` |
| [DynamoDB](../building-blocks/data-connectors/dynamodb.md) | DynamoDB Streams | `refresh_mode: changes` |
| [Apache Kafka](../building-blocks/data-connectors/kafka.md) | Event stream consumption | `refresh_mode: append` |
| [Debezium](../building-blocks/data-connectors/debezium.md) | Debezium change events over Kafka | `refresh_mode: changes` |

{% hint style="info" %}
Sources without a native Spice change feed — including MySQL and SQL Server — replicate through [Debezium](../building-blocks/data-connectors/debezium.md) over Kafka.
{% endhint %}

### Configuration

A replicated dataset needs a `primary_key` so that updates and deletes can be matched to existing rows, and an `on_conflict` rule so that repeated keys upsert rather than duplicate.

```yaml
datasets:
  - from: postgres:public.orders
    name: orders
    params:
      pg_host: postgres.example-org.com
      pg_port: '5432'
      pg_user: spice
      pg_pass: ${secrets:pg_pass}
      pg_db: myapp
      pg_sslmode: verify-full
    acceleration:
      enabled: true
      engine: cayenne
      mode: file
      refresh_mode: changes
      primary_key: id
      on_conflict:
        id: upsert
```

On startup Spice loads an initial snapshot of the table, then switches to streaming changes. No `refresh_check_interval` is required — changes are applied as they arrive rather than on a poll.

Any accelerator engine can back a replicated dataset. [Cayenne](../building-blocks/data-accelerators/cayenne.md) is built for this workload, sustaining a high-throughput change feed while serving analytical scans from the same table.

### PostgreSQL prerequisites

Logical replication must be enabled on the source server:

```
wal_level = logical
max_replication_slots = 10
max_wal_senders = 10
```

Each replicated table needs a primary key, or `REPLICA IDENTITY FULL`, so that updates and deletes carry enough information to identify the affected row:

```sql
ALTER TABLE public.orders REPLICA IDENTITY FULL;
```

The connecting role needs the `REPLICATION` attribute, plus `SELECT` on the replicated tables. Spice creates and manages its own replication slot and publication.

{% hint style="warning" %}
An inactive replication slot causes the source server to retain write-ahead log segments indefinitely, which can exhaust disk on the primary. Drop the slot on the source if a replicated dataset is removed permanently.
{% endhint %}

### Handling deletes

Replication propagates hard deletes, which sets it apart from incremental ingestion. A `DELETE` on the source removes the row from the replica on the next change event, with no reconciling full refresh and no soft-delete convention in the source schema.

Sources that expose no change feed at all — HTTP APIs, for example — use [incremental ingestion](data-acceleration/README.md#incremental-ingestion) with `refresh_mode: append` instead, where deletes are handled by soft-delete tombstones or a periodic full refresh.

### Related

* [Analytics Replica](../use-cases/analytics-replica.md) — the deployment pattern built on replication
* [Data Acceleration](data-acceleration/README.md) — refresh modes, incremental ingestion, and retention
* [Database CDN](../use-cases/database-cdn.md) — colocating a hot working set with an application
* [Change data capture](https://spiceai.org/docs/features/cdc) in the Spice.ai OSS documentation
