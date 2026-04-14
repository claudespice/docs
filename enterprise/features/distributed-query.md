---
description: Active-active distributed query with Apache Ballista for multi-node Spice.ai clusters.
icon: diagram-project
---

# Distributed Query

Spice.ai Enterprise supports distributed query execution, built on [Apache Ballista](https://github.com/apache/datafusion-ballista), for horizontally scaling SQL query workloads across multiple nodes.

## Architecture

A distributed query cluster consists of:

- **Schedulers** — Coordinate query planning, catalog synchronization, and work distribution.
- **Executors** — Execute query stages, perform shuffles, and return results.
- **Object store** — S3-compatible storage for shared state and shuffle data.

```
     ┌──────────────────────────────────────┐
     │           Load Balancer              │
     └──────────────────────────────────────┘
                        │
     ┌──────────────────┼──────────────────┐
     ▼                  ▼                  ▼
┌──────────┐     ┌──────────┐     ┌──────────┐
│Scheduler │     │Scheduler │     │Scheduler │◄──► Object Store
└──────────┘     └──────────┘     └──────────┘
     ▲                  ▲                  ▲
     │                  │                  │
┌──────────┐     ┌──────────┐     ┌──────────┐
│ Executor │     │ Executor │     │ Executor │
└──────────┘     └──────────┘     └──────────┘
```

## Active-Active HA

Multiple schedulers can run simultaneously in active-active mode:

- **Shared state** is maintained in an S3-compatible object store with conditional writes for conflict resolution.
- **Scheduler discovery** uses shared state registration — no external coordination service required.
- **Executor discovery** is executor-initiated — executors connect to all known schedulers.
- **Jobs execute exactly once** across the cluster.

## Deployment

### Kubernetes (SpicepodCluster)

The recommended deployment method is the [SpicepodCluster](../kubernetes/spicepodcluster.md) CRD, which automates cluster topology, mTLS, and lifecycle management.

### Manual (CLI)

For non-Kubernetes deployments, configure roles via CLI arguments:

```bash
# Scheduler
spiced --role scheduler \
  --node-mtls-ca-certificate-file ca.pem \
  --node-mtls-certificate-file scheduler.pem \
  --node-mtls-key-file scheduler-key.pem \
  --node-advertise-address scheduler1.cluster.local

# Executor
spiced --role executor \
  --node-mtls-ca-certificate-file ca.pem \
  --node-mtls-certificate-file executor.pem \
  --node-mtls-key-file executor-key.pem \
  --scheduler-address https://scheduler1.cluster.local:50052 \
  --node-advertise-address executor1.cluster.local
```

## Catalog and UDF Synchronization

In a distributed cluster, executors automatically synchronize catalogs and UDFs from the scheduler:

- **`GetCatalog`** — Fetches catalog, schema, and table metadata with Arrow schemas.
- **`GetFunctions`** — Fetches UDF signatures, return types, and documentation.

This ensures query planning and execution use a consistent view of the data model across all nodes.

## Execution Modes

| Mode             | Description                                                                      |
| ---------------- | -------------------------------------------------------------------------------- |
| **Synchronous**  | Client waits for the query to complete and receives results directly.            |
| **Asynchronous** | Client submits a query and polls for results. Suitable for long-running queries. |

## Performance

Distributed query with Apache Ballista achieves **2.9x overall speedup** vs single-node DataFusion on TPC-H SF100 benchmarks, with shuffle data spilled to disk and failed stages retried without restarting.

## Distributed Capabilities

The following operations are distributed across executor nodes:

- SQL query execution with shuffle-based data exchange
- Cayenne data-local query routing
- Partition-aware write-through (INSERT, UPDATE, DELETE)
- MERGE INTO with distributed duplicate key detection
- Distributed `runtime.task_history`
- Executor DDL sync on connect
