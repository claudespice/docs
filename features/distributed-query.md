---
description: Distributed multi-node SQL query execution
icon: sitemap
---

# Distributed Query

Distributed query enables multi-node SQL execution across a cluster of Spice scheduler and executor nodes. Built on [Apache Ballista](https://github.com/apache/datafusion-ballista), it distributes query execution across multiple executor nodes for higher throughput on large-scale analytical workloads.

{% hint style="warning" %}
Distributed query is in preview and should not be used in production workloads.
{% endhint %}

## Features

* **Distributed SQL execution**: Queries are planned by the scheduler and executed in parallel across multiple executor nodes
* **Automatic task scheduling**: The scheduler decomposes queries into stages and assigns individual tasks to executors using pull-based scheduling
* **Shuffle-based execution**: Intermediate results are shuffled between query stages via disk-backed storage, enabling fault-tolerant retry of failed stages
* **Mutual TLS (mTLS)**: Cluster-internal communication is secured with mTLS by default
* **Async queries API**: Submit long-running queries asynchronously and retrieve results via HTTP REST or Arrow Flight (see [Async Queries API](async-queries-api.md))
* **Dynamic executor scaling**: Executors can be added or removed at runtime
* **Automatic config sync**: Executors fetch the Spicepod configuration and secrets from the scheduler on startup

## Architecture

A distributed Spice cluster consists of two node roles:

### Scheduler

The scheduler is the control plane of the cluster. It:

* Serves as the client-facing entry point (HTTP API and Flight endpoints)
* Plans incoming queries using DataFusion and decomposes them into distributed execution stages
* Dispatches tasks to executors via pull-based scheduling
* Provides the Spicepod configuration and secrets to executors via the internal `ClusterService` gRPC API
* Manages async query jobs (state, results, cleanup) when `scheduler.state_location` is configured

### Executor

Executors are the data plane workers. They:

* Register with the scheduler on startup
* Poll for available tasks from the scheduler
* Execute query fragments (tasks) and write shuffle output to local disk
* Fetch dataset configuration and secrets from the scheduler automatically
* Can be scaled horizontally — add more executors for higher parallelism

### Port Architecture

| Port  | Visibility | Services                         | mTLS     |
| ----- | ---------- | -------------------------------- | -------- |
| 50051 | Public     | Arrow Flight (user queries)      | Optional |
| 8090  | Public     | HTTP API (REST, health checks)   | Optional |
| 9090  | Public     | Prometheus metrics               | No       |
| 50052 | Internal   | Scheduler gRPC, `ClusterService` | Required |

The internal cluster port (50052) is separate from the public-facing ports to isolate sensitive operations like secret distribution.

## Configuration

### Scheduler Node

Start the scheduler with `--role scheduler`:

```bash
spiced --role scheduler \
  --http 127.0.0.1:8090 \
  --flight 127.0.0.1:50051 \
  --node-bind-address 0.0.0.0:50052 \
  --node-advertise-address 127.0.0.1 \
  --node-mtls-ca-certificate-file ~/.spice/pki/ca.crt \
  --node-mtls-certificate-file ~/.spice/pki/scheduler1.crt \
  --node-mtls-key-file ~/.spice/pki/scheduler1.key
```

The scheduler reads the `spicepod.yaml` in the current directory for dataset definitions.

### Executor Node

Start each executor with `--role executor` (or just `--scheduler-address`, which implies executor):

```bash
spiced --role executor \
  --scheduler-address 127.0.0.1:50052 \
  --http 127.0.0.1:9091 \
  --node-bind-address 0.0.0.0:50062 \
  --node-advertise-address 127.0.0.1 \
  --node-mtls-ca-certificate-file ~/.spice/pki/ca.crt \
  --node-mtls-certificate-file ~/.spice/pki/executor1.crt \
  --node-mtls-key-file ~/.spice/pki/executor1.key
```

Executors do **not** need a `spicepod.yaml` — they receive the configuration from the scheduler.

### CLI Flags

| Flag                                | Default         | Description                                                                                           |
| ----------------------------------- | --------------- | ----------------------------------------------------------------------------------------------------- |
| `--role <scheduler\|executor>`      | _none_          | Explicit node role. If omitted but `--scheduler-address` is set, executor is implied.                 |
| `--scheduler-address <URL>`         | _none_          | Scheduler's internal cluster address. Required for executors. Scheme inferred from TLS config.        |
| `--node-bind-address <ADDR>`        | `0.0.0.0:50052` | Bind address for the internal cluster gRPC service.                                                   |
| `--node-advertise-address <HOST>`   | _none_          | Hostname/IP this node advertises to other nodes in the cluster.                                       |
| `--node-mtls-ca-certificate-file`   | _none_          | CA certificate file for validating peer certificates.                                                 |
| `--node-mtls-certificate-file`      | _none_          | This node's TLS certificate (signed by the CA).                                                       |
| `--node-mtls-key-file`              | _none_          | This node's private key file.                                                                         |
| `--allow-insecure-connections`      | `false`         | Disables mTLS requirement. **Development/testing only — never use in production.**                    |

### Spicepod Configuration

```yaml
version: v1
kind: Spicepod
name: my-cluster

runtime:
  scheduler:
    state_location: "s3://my-bucket/spice-state"   # Required for async queries API
    params:                                          # Optional: object store credentials
      access_key_id: "${env:AWS_ACCESS_KEY_ID}"
      secret_access_key: "${env:AWS_SECRET_ACCESS_KEY}"

datasets:
  - from: s3://my-data-bucket/events/
    name: events
    params:
      file_format: parquet

  - from: postgres:public.orders
    name: orders
    params:
      pg_host: db.example.com
      pg_port: '5432'
      pg_db: mydb
      pg_user: spice
      pg_pass: '${secrets:pg_password}'
```

The `scheduler.state_location` is a shared object store URI (S3, GCS, Azure Blob, or local filesystem) used to persist async query job state and result chunks. It is required to enable the `/v1/queries` [async queries API](async-queries-api.md).

## mTLS Setup

Mutual TLS is required by default for all cluster-internal communication. The Spice CLI provides helpers to generate development certificates.

### Step 1: Initialize the Certificate Authority

```bash
spice cluster tls init
```

Creates `~/.spice/pki/ca.crt` and `~/.spice/pki/ca.key`.

### Step 2: Generate Node Certificates

```bash
spice cluster tls add scheduler1
spice cluster tls add executor1
spice cluster tls add executor2
```

Each command creates `~/.spice/pki/{name}.crt` and `~/.spice/pki/{name}.key`, signed by the CA.

For production, use certificates from your organization's PKI or a tool like `cert-manager`.

### Disabling mTLS (Development Only)

For local development, mTLS can be disabled:

```bash
spiced --role scheduler --allow-insecure-connections ...
spiced --role executor --allow-insecure-connections ...
```

{% hint style="danger" %}
Never disable mTLS in production. The internal cluster port exposes sensitive operations including secret distribution.
{% endhint %}

## Querying

### Synchronous Queries

Use `spice sql` or the Flight/HTTP SQL APIs. Queries submitted to the scheduler are automatically distributed across executors:

```bash
spice sql
```

```sql
SELECT status, COUNT(*) as cnt FROM events GROUP BY status ORDER BY cnt DESC LIMIT 10;
```

The scheduler plans the query, assigns tasks to executors, collects results via shuffle, and returns the final result to the client.

### Async Queries API

For long-running queries, use the [async queries API](async-queries-api.md). Queries are submitted, executed in the background, and results are retrieved when ready.

{% hint style="info" %}
The async queries API requires `scheduler.state_location` to be configured.
{% endhint %}

#### HTTP REST

Submit a query:

```bash
curl -s http://127.0.0.1:8090/v1/queries \
  -H "Content-Type: application/json" \
  -d '{"sql": "SELECT * FROM events WHERE date > '\''2025-01-01'\''"}' | jq .
```

```json
{
  "query_id": "01ABC-DEF-456-7890AB",
  "status": "PENDING",
  "status_url": "/v1/queries/01ABC-DEF-456-7890AB/status",
  "results_url": "/v1/queries/01ABC-DEF-456-7890AB/results"
}
```

Poll for completion:

```bash
curl -s http://127.0.0.1:8090/v1/queries/01ABC-DEF-456-7890AB/status | jq .
```

Retrieve results (paginated in chunks of 10,000 rows):

```bash
curl -s http://127.0.0.1:8090/v1/queries/01ABC-DEF-456-7890AB/results | jq .
```

Cancel a query:

```bash
curl -s -X POST http://127.0.0.1:8090/v1/queries/01ABC-DEF-456-7890AB/cancel | jq .
```

List all queries:

```bash
curl -s "http://127.0.0.1:8090/v1/queries?status=running" | jq .
```

See the full [Async Queries API Reference](async-queries-api.md) for detailed endpoint documentation, request/response schemas, error codes, and Arrow Flight actions.

#### CLI

The `spice query` command provides a CLI wrapper around the async queries API:

```bash
# Submit and wait for results
spice query "SELECT COUNT(*) FROM events;"

# Submit without waiting
spice query "SELECT * FROM events;" --no-wait

# Check status
spice query status 01ABC-DEF-456-7890AB

# Get results
spice query results 01ABC-DEF-456-7890AB

# Cancel
spice query cancel 01ABC-DEF-456-7890AB

# Interactive REPL
spice query
```

See the full [spice query CLI Reference](spice-query.md) for all options, subcommands, and REPL commands.

#### Async Query Lifecycle

```
PENDING → RUNNING → SUCCEEDED → CLOSED (after 12h TTL)
                   → FAILED
                   → CANCELLED
```

| Status      | Description                                      |
| ----------- | ------------------------------------------------ |
| `PENDING`   | Query is queued but not yet executing            |
| `RUNNING`   | Query is actively executing across the cluster   |
| `SUCCEEDED` | Query completed, results available for retrieval |
| `FAILED`    | Query execution failed (see error details)       |
| `CANCELLED` | Query was cancelled by client                    |
| `CLOSED`    | Results expired and cleaned up (12-hour TTL)     |

#### Async Query Options

| Option            | Type    | Description                                                     |
| ----------------- | ------- | --------------------------------------------------------------- |
| `timeout_seconds` | integer | Maximum execution time. Query is auto-cancelled on timeout.     |
| `maximum_size`    | integer | Maximum result size in bytes. Query fails if results exceed it. |
| `parameters`      | array   | Bind variables for parameterized queries (`$1`, `$2`, ...).     |

## Performance Considerations

* **Use distributed mode for analytical/batch workloads**: Queries that scan large datasets across multiple data sources benefit most. For sub-second latency requirements, use single-node Spice with acceleration.
* **Co-locate nodes**: Deploy scheduler and executors in the same datacenter or region to minimize network latency during shuffle.
* **Scale executors for parallelism**: More executors = more parallel task execution. Each executor can run multiple tasks concurrently.
* **Monitor shuffle disk usage**: Executors write intermediate shuffle data to local disk (`/tmp` by default). Ensure sufficient disk space for large queries.
* **Partition source data**: Well-partitioned source data (e.g., Hive-partitioned Parquet in S3) enables more efficient distributed scans.

## Limitations

* **Preview feature**: Distributed query is in preview and not recommended for production workloads
* **No accelerated dataset distribution**: Materialized/accelerated tables are single-node only. Distributed mode targets federated data sources.
* **Higher latency than single-node**: Scheduling overhead, network serialization, and disk-based shuffle add latency compared to single-node execution
* **Single scheduler**: Ballista uses a single active scheduler. High-availability scheduler support is planned.
* **Executor disk requirements**: Local disk space is needed for shuffle intermediate data between query stages
* **Credential propagation**: AWS/Azure/GCS credentials are automatically propagated to executors; other credential types may require manual configuration on each executor node

### Async Queries Limitations

* Only available on scheduler nodes (`--role scheduler`)
* Requires `scheduler.state_location` to be configured
* Result TTL is fixed at 12 hours (not configurable per-query)
* Chunk size is fixed at 10,000 rows (not configurable per-query)
* The `format` query parameter on the results endpoint is declared but not yet implemented (results are always JSON over HTTP, Arrow IPC over Flight)

## References

* [Async Queries API Reference](async-queries-api.md)
* [spice query CLI Reference](spice-query.md)
* [Distributed Query Cookbook Recipe](https://github.com/spiceai/cookbook/tree/trunk/distributed)
* [Async Queries Cookbook Recipe](https://github.com/spiceai/cookbook/tree/trunk/async-queries)
* [Apache Ballista](https://github.com/apache/datafusion-ballista)
