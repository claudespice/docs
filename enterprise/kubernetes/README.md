---
description: Spice.ai Kubernetes Operator for automated deployment and lifecycle management.
icon: ship
---

# Kubernetes Operator

The Spice.ai Kubernetes Operator automates the deployment, scaling, and lifecycle management of Spice.ai workloads on Kubernetes. It provides two Custom Resource Definitions (CRDs):

- **`SpicepodSet`** (`spice.ai/v1`) — Deploys and manages Spicepod replicas as a `Deployment` or per-replica `StatefulSet`s.
- **`SpicepodCluster`** (`spice.ai/v1alpha1`) — Deploys a distributed query cluster with scheduler and executor nodes, secured with auto-provisioned mTLS certificates.

## Installation

The Spice Kubernetes Operator is distributed exclusively through the [AWS Marketplace](../deployment/aws-marketplace.md) Spice.ai Enterprise listing. Subscribe and authenticate to the Marketplace ECR registry, then install:

### Helm

```bash
helm install spiceai-operator \
  oci://709825985650.dkr.ecr.us-east-1.amazonaws.com/spice-ai/charts/spiceai-operator
```

### Docker

```bash
docker pull 709825985650.dkr.ecr.us-east-1.amazonaws.com/spice-ai/spiceai-operator:latest
```

### Helm Values

| Parameter                    | Description                           | Default                                                                  |
| ---------------------------- | ------------------------------------- | ------------------------------------------------------------------------ |
| `image.repository`           | Operator image repository             | `709825985650.dkr.ecr.us-east-1.amazonaws.com`                            |
| `image.name`                 | Operator image name                   | `spice-ai/spiceai-operator`                                              |
| `image.tag`                  | Operator image tag                    | `latest`                                                                 |
| `image.pullSecrets`          | Image pull secrets                    | —                                                                        |
| `installCRDs`                | Install/update CRDs with the chart    | `true`                                                                   |
| `serviceAccount.annotations` | Annotations (e.g. for IRSA)           | `{}`                                                                     |
| `servicemonitor.enabled`     | Enable Prometheus `ServiceMonitor`    | `false`                                                                  |
| `cluster.clusterDomain`      | Kubernetes cluster domain             | `cluster.local`                                                          |
| `telemetryProperties`        | Key/value pairs for the Spice runtime | —                                                                        |

## Managed Resources

For each `SpicepodSet`, the operator creates and manages:

1. **ServiceAccount** — When `service_account.enabled` and `service_account.create` are `true`.
2. **Role** — Scoped permissions for Spicepod pods.
3. **RoleBinding** — Binds the Role to the ServiceAccount.
4. **ConfigMap** — Stores the `spicepod` YAML, mounted into the pod.
5. **NetworkPolicy** — Controls ingress/egress traffic.
6. **Deployment / StatefulSet(s)** — The primary workload.
7. **Service** — `ClusterIP` service exposing HTTP (`8090`), Flight (`50051`), and metrics (`9090`).

## Adaptive Workload Deployment

For simple stateless cases (no `volume`, no `cluster`, `replicas <= 1`), the operator uses a `Deployment`. When volumes, cluster mode, or multiple replicas are configured, it creates per-replica `StatefulSet`s with stable pod identities and ordered startup.

## Features

| Feature                                    | SpicepodSet | SpicepodCluster |
| ------------------------------------------ | :---------: | :-------------: |
| Adaptive workload deployment               |      ✓      |        ✓        |
| High-availability rollouts                 |      ✓      |        ✓        |
| Configurable update strategies             |      ✓      |        ✓        |
| Persistent volume with auto-resize         |      ✓      |        ✓        |
| Zero-replica pausing                       |      ✓      |        ✓        |
| Crashloop protection                       |      ✓      |        ✓        |
| Forced rollouts via annotations/labels     |      ✓      |        ✓        |
| Network policy management                  |      ✓      |        ✓        |
| Service account configuration (incl. IRSA) |      ✓      |        ✓        |
| Health probe customization                 |      ✓      |        ✓        |
| Pod scheduling (affinity, tolerations)     |      ✓      |        ✓        |
| Automatic mTLS certificates                |      —      |        ✓        |
| Distributed scheduler/executor topology    |      —      |        ✓        |
| Prometheus metrics & ServiceMonitor        |      ✓      |        ✓        |

## Operator CLI

### `crd` — Output or apply CRD definitions

```bash
spiceai-operator crd              # Print CRD YAML to stdout
spiceai-operator crd --apply      # Apply CRDs to the current cluster
spiceai-operator crd --output FILE
```

### `run` — Start the operator controller

| Flag                                  | Default                   | Description                          |
| ------------------------------------- | ------------------------- | ------------------------------------ |
| `--enable-leader-election`            | `false`                   | Enable leader election for HA        |
| `--http-endpoint`                     | `0.0.0.0:8090`            | Operator HTTP API address            |
| `--metrics-endpoint`                  | `0.0.0.0:9090`            | Prometheus metrics address           |
| `--operator-namespace`                | `spiceai-operator-system` | Namespace for the operator           |
| `--cluster-domain`                    | `cluster.local`           | Kubernetes cluster domain            |
| `--pause-crashlooping-pods-threshold` | `10`                      | Dead pod observations before pausing |
| `--telemetry-properties KEY=VALUE`    | —                         | Key/value pairs for the runtime      |
| `--verbose`                           | `false`                   | Enable debug-level logging           |

## Operator HTTP API

| Endpoint                                   | Method | Description                    |
| ------------------------------------------ | ------ | ------------------------------ |
| `/health`                                  | GET    | Health check — returns `OK`    |
| `/{namespace}/{name}`                      | GET    | Pod status for a `SpicepodSet` |
| `/{namespace}/{name}?kind=SpicepodCluster` | GET    | Status for a `SpicepodCluster` |

## Upgrading

```bash
helm upgrade spiceai-operator \
  oci://709825985650.dkr.ecr.us-east-1.amazonaws.com/spice-ai/charts/spiceai-operator \
  --values my-values.yaml
```
