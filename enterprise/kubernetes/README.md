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

| Parameter                              | Description                                                                       | Default                                                       |
| -------------------------------------- | --------------------------------------------------------------------------------- | ------------------------------------------------------------- |
| `image.repository`                     | Operator image repository                                                         | `709825985650.dkr.ecr.us-east-1.amazonaws.com`                |
| `image.name`                           | Operator image name                                                               | `spice-ai/spiceai-operator`                                   |
| `image.tag`                            | Operator image tag                                                                | `latest`                                                      |
| `image.pullPolicy`                     | Operator image pull policy                                                        | `IfNotPresent`                                                |
| `image.pullSecrets`                    | Image pull secrets                                                                | —                                                             |
| `installCRDs`                          | Install/update CRDs with the chart                                                | `true`                                                        |
| `serviceAccount.create`                | Create a ServiceAccount for the operator                                          | `true`                                                        |
| `serviceAccount.name`                  | ServiceAccount name                                                               | (chart name)                                                  |
| `serviceAccount.annotations`           | Annotations for the operator ServiceAccount (e.g. for IRSA)                       | `{}`                                                          |
| `resources`                            | CPU/memory `requests` and `limits` for the operator container                     | —                                                             |
| `nodeSelector` / `tolerations` / `affinity` | Operator pod scheduling                                                      | —                                                             |
| `serviceMonitor.enabled`               | Enable Prometheus `ServiceMonitor`                                                | `false`                                                       |
| `serviceMonitor.interval`              | Scrape interval                                                                   | `30s`                                                         |
| `cluster.domain`                       | Kubernetes cluster domain for internal DNS                                        | `cluster.local`                                               |
| `pauseCrashloopingPodsThreshold`       | Crashlooping pod threshold before pausing a `SpicepodSet` (`0` disables)          | `10`                                                          |
| `sidecarInjector.defaultImage`         | Default image used by the sidecar injector when no per-Pod override is set        | (operator default)                                            |
| `sidecarInjector.defaultImagePullPolicy` | Default pull policy for injected sidecars                                       | (operator default)                                            |
| `telemetryProperties`                  | Key/value pairs forwarded to the Spice runtime as telemetry properties            | `{}`                                                          |

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

For simple stateless cases (no `volume`, no `cluster`, `replicas <= 1`), the operator uses a `Deployment`. When `volume`, `cluster`, or `replicas > 1` is configured, the operator creates per-replica `StatefulSet`s with stable pod identities and ordered startup. Switching between modes is automatic when the spec changes.

## Features

| Feature                                    | SpicepodSet | SpicepodCluster |
| ------------------------------------------ | :---------: | :-------------: |
| Adaptive workload deployment               |      ✓      |        ✓        |
| Update strategies (`RollingOrdered`, `RollingParallel`, `Parallel`, `BlueGreen`) | ✓ | ✓ |
| Instant rollback via `spice.ai/rollback`   |      ✓      |        ✓        |
| Persistent volume with auto-resize         |      ✓      |        ✓        |
| Zero-replica pausing                       |      ✓      |        ✓        |
| Crashloop protection                       |      ✓      |        ✓        |
| Forced rollouts via annotations/labels     |      ✓      |        ✓        |
| Network policy management                  |      ✓      |        ✓        |
| Service account configuration (incl. IRSA) |      ✓      |        ✓        |
| Health probe customization                 |      ✓      |        ✓        |
| Pod scheduling (affinity, tolerations)     |      ✓      |        ✓        |
| Sidecar injection via Pod annotations      |      —      |        —        |
| Automatic mTLS certificates                |      —      |        ✓        |
| Distributed scheduler/executor topology    |      —      |        ✓        |
| Prometheus metrics & ServiceMonitor        |      ✓      |        ✓        |

## Sidecar Injection

The operator can inject a Spice sidecar into any standard Kubernetes Pod by annotating the Pod template with `spice.ai/inject` and pointing it at a `ConfigMap` in the same namespace that holds your `spicepod.yaml`. For the common case, `spice.ai/inject` can name the ConfigMap directly:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: demo-spice-config
data:
  spicepod.yaml: |
    name: demo-sidecar
    kind: Spicepod
    version: v1
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-with-spice
spec:
  replicas: 1
  selector:
    matchLabels: { app: app-with-spice }
  template:
    metadata:
      labels: { app: app-with-spice }
      annotations:
        spice.ai/inject: demo-spice-config
    spec:
      containers:
        - name: app
          image: nginx:stable
```

### Supported annotations

| Annotation                     | Default          | Description                                                                                  |
| ------------------------------ | ---------------- | -------------------------------------------------------------------------------------------- |
| `spice.ai/inject`              | —                | `true`/`enabled`, `false`/`disabled`, **or** a ConfigMap name (one-annotation shorthand).     |
| `spice.ai/spicepod-configmap`  | —                | ConfigMap holding the spicepod. Optional when `spice.ai/inject` already names the ConfigMap. |
| `spice.ai/spicepod-key`        | `spicepod.yaml`  | Key inside the ConfigMap data.                                                               |
| `spice.ai/image`               | install default  | Override the injected Spice image.                                                           |
| `spice.ai/image-pull-policy`   | install default  | Override the injected image pull policy.                                                     |
| `spice.ai/http-port`           | `18090`          | Sidecar HTTP port.                                                                           |
| `spice.ai/flight-port`         | `15051`          | Sidecar Arrow Flight port.                                                                   |
| `spice.ai/metrics-port`        | `19090`          | Sidecar Prometheus metrics port.                                                             |

{% hint style="info" %}
Injection runs on Pod creation, so place these annotations on the controller's Pod template (e.g. `Deployment.spec.template.metadata.annotations`) and `kubectl rollout restart` to pick up changes. The ConfigMap must already exist when the Pod is created. The webhook rejects Pods whose existing container ports collide with the requested sidecar ports.
{% endhint %}

Cluster operators can set defaults globally via Helm values `sidecarInjector.defaultImage` and `sidecarInjector.defaultImagePullPolicy`, with per-workload annotations as overrides.

## Operator CLI

### `crd` — Output or apply CRD definitions

```bash
spiceai-operator crd              # Print CRD YAML to stdout
spiceai-operator crd --apply      # Apply CRDs to the current cluster
spiceai-operator crd --output FILE
```

### `run` — Start the operator controller

| Flag                                  | Default                   | Description                                                  |
| ------------------------------------- | ------------------------- | ------------------------------------------------------------ |
| `--http-endpoint`                     | `0.0.0.0:8090`            | Operator HTTP API bind address                                |
| `--metrics-endpoint`                  | `0.0.0.0:9090`            | Prometheus metrics bind address                              |
| `--operator-namespace`                | `spiceai-operator-system` | Namespace for the operator (used for cluster-shared secrets) |
| `--cluster-domain`                    | `cluster.local`           | Kubernetes cluster domain                                    |
| `--pause-crashlooping-pods-threshold` | `10`                      | Dead pod observations before pausing (`0` disables)          |
| `--telemetry-properties KEY=VALUE`    | —                         | Key/value pairs forwarded to the Spice runtime               |
| `--verbose`                           | `false`                   | Enable debug-level logging                                   |

### `json-schema` — Output the OpenAPI v3 JSON schema for the `SpicepodSet` CRD

```bash
spiceai-operator json-schema              # Print to stdout
spiceai-operator json-schema --output FILE
```

## Operator HTTP API

| Endpoint                                   | Method | Description                                                                              |
| ------------------------------------------ | ------ | ---------------------------------------------------------------------------------------- |
| `/health`                                  | GET    | Health check — returns `OK`                                                              |
| `/{namespace}/{name}`                      | GET    | Pod status for a `SpicepodSet`                                                           |
| `/{namespace}/{name}?kind=SpicepodCluster` | GET    | Pod status for a `SpicepodCluster`                                                       |

The pod-status response includes per-pod details (name, UID, phase, IP, port, start time, Spiced health/readiness, and any error reason/message). For paused `SpicepodSet`s (`replicas: 0`), the response includes `paused: true` with a `pauseReason`.

## Upgrading

```bash
helm upgrade spiceai-operator \
  oci://709825985650.dkr.ecr.us-east-1.amazonaws.com/spice-ai/charts/spiceai-operator \
  --values my-values.yaml
```
