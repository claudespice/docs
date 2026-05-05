---
description: SpicepodSet CRD reference for deploying and managing Spicepod replicas.
icon: layer-group
---

# SpicepodSet

A `SpicepodSet` (`spice.ai/v1`) deploys and manages one or more Spicepod replicas. The operator handles the full lifecycle: creating the workload, rolling updates, volume management, health monitoring, and crashloop protection.

The operator chooses the underlying workload type adaptively:

- A single `Deployment` for the simple case (no `volume`, no `cluster`, `replicas <= 1`).
- Per-replica `StatefulSet`s when `volume`, `cluster`, or `replicas > 1` is configured, providing stable pod identities and ordered startup.

## Minimal Example

```yaml
apiVersion: spice.ai/v1
kind: SpicepodSet
metadata:
  name: my-spicepod
  namespace: default
spec:
  replicas: 1
  spicepod:
    name: my-spicepod
    kind: Spicepod
    version: v1
```

The `spicepod` field accepts the inline Spicepod definition (datasets, catalogs, models, views, etc.) and is preserved as-is by the operator.

## Container Image

```yaml
spec:
  image:
    registry: 709825985650.dkr.ecr.us-east-1.amazonaws.com
    name: spice-ai/spiceai-enterprise-byol
    tag: latest-models
    pullPolicy: IfNotPresent          # Always | Never | IfNotPresent
    pullSecret: my-pull-secret
```

| Field        | Default                                     | Description                                                              |
| ------------ | ------------------------------------------- | ------------------------------------------------------------------------ |
| `tag`        | `latest-models`                             | Image tag.                                                               |
| `name`       | `spiceai/spiceai`                           | Image name.                                                              |
| `registry`   | Docker Hub                                  | Image registry.                                                          |
| `pullPolicy` | `Always` for `:latest`, else `IfNotPresent` | Image pull policy.                                                       |
| `pullSecret` | —                                           | Name of a Kubernetes Secret holding credentials for a private registry.  |

## Ports

```yaml
spec:
  httpPort: 8090
  flightPort: 50051
  metricsPort: 9090
```

| Field         | Default | Description                |
| ------------- | ------- | -------------------------- |
| `httpPort`    | `8090`  | HTTP API port.             |
| `flightPort`  | `50051` | Apache Arrow Flight port.  |
| `metricsPort` | `9090`  | Prometheus metrics port.   |

The Service exposes fixed ports `8080` (HTTP), `50051` (Flight), and `9090` (metrics) and maps `targetPort` to the configured Spiced ports above.

## Resources

```yaml
spec:
  resources:
    requests:
      cpu: 200m
      memory: 1Gi
    limits:
      cpu: "2"
      memory: 4Gi
```

## Environment Variables and Secrets

```yaml
spec:
  env:
    - name: SPICE_LOG_LEVEL
      value: debug
    - name: MY_SECRET
      valueFrom:
        secretKeyRef:
          name: my-secret
          key: api-key
  envFromSource:
    - configMapRef:
        name: my-config
    - secretRef:
        name: my-secret
```

## Persistent Storage

Enable persistent volumes with automatic resizing:

```yaml
spec:
  volume:
    storageClassName: standard
    storageRequests: 10Gi
```

{% hint style="warning" %}
Volume shrinking is not supported. Decreasing `storageRequests` has no effect on existing PVCs.
{% endhint %}

When `volume` is configured, the operator deploys per-replica `StatefulSet`s with `PersistentVolumeClaim`s. Increasing `storageRequests` triggers automatic PVC resizing.

## Service Account

```yaml
# Operator-managed
spec:
  serviceAccount:
    enabled: true
    create: true

# Bring your own
spec:
  serviceAccount:
    enabled: true
    create: false
    name: my-existing-service-account

# With IRSA annotations
spec:
  serviceAccount:
    enabled: true
    create: true
    annotations:
      eks.amazonaws.com/role-arn: arn:aws:iam::123456789012:role/spice-ai-role
```

| Field         | Default            | Description                                                                                    |
| ------------- | ------------------ | ---------------------------------------------------------------------------------------------- |
| `enabled`     | `false`            | Whether to use a ServiceAccount.                                                               |
| `create`      | `false`            | Whether to create a new ServiceAccount. If `false`, set `name` to reference an existing one.   |
| `name`        | `SpicepodSet` name | ServiceAccount name; required when `create: false`.                                            |
| `annotations` | —                  | Annotations applied **only** to the ServiceAccount (ideal for IRSA `eks.amazonaws.com/role-arn`). |

## Update Strategies

```yaml
spec:
  updateStrategy:
    type: RollingOrdered    # default
```

| Strategy                 | Behavior                                                                                                                            |
| ------------------------ | ----------------------------------------------------------------------------------------------------------------------------------- |
| **`RollingOrdered`** (default) | Updates pods one at a time in ordinal order, waiting for each to become Ready before proceeding.                          |
| **`RollingParallel`**    | Updates pods in parallel. Set `maxUnavailable` to bound concurrent unavailable pods.                                                |
| **`Parallel`**           | Updates all pods simultaneously with no availability constraints.                                                                   |
| **`BlueGreen`**          | Brings up a parallel `StatefulSet` for the new generation, then atomically switches Service traffic once **all** new-generation pods are Ready. PVCs are ephemeral across generations. |

```yaml
spec:
  replicas: 5
  updateStrategy:
    type: RollingParallel
    maxUnavailable: 2
    minReadyForCutover: 3   # optional
```

### `minReadyForCutover`

Switches the Service to the new generation as soon as `minReadyForCutover` new-generation pods are Ready, instead of waiting for all of them.

- **For `BlueGreen` and instant-rollback**: switches the version-pinned Service selector and promotes `status.activeVersion` early.
- **For `RollingOrdered` / `RollingParallel`**: causes the Service object to be created earlier in the rollout (selector is not version-pinned, so traffic is already routed to all matching Ready pods once the Service exists).
- **Ignored** for `Parallel`. A value of `0` is treated as unset. Values larger than `replicas` collapse to "wait for all".

## Instant Rollback

Retain the previous-generation pods after a rollout so traffic can be cut back to them instantly without rolling pods.

```yaml
spec:
  updateStrategy:
    type: BlueGreen
  instantRollback:
    enabled: true
    retentionPeriodSeconds: 900   # default 15 minutes
```

After a successful rollout, set the rollback annotation on the `SpicepodSet` to swap traffic back to the standby:

```bash
kubectl annotate spicepodset my-spicepod spice.ai/rollback=true --overwrite
```

The operator switches the Service to the standby pods, clears the annotation, and tracks `status.activeVersion` / `status.standbyVersion` / `status.standbyExpiresAt`. Standby pods are torn down after `retentionPeriodSeconds` if no rollback occurs.

{% hint style="info" %}
Combine `instantRollback` with `BlueGreen` for the canonical zero-downtime production rollout pattern.
{% endhint %}

## Scaling and Pausing

Set `replicas: 0` to pause the workload while retaining supporting resources (Service, ConfigMap, NetworkPolicy, ServiceAccount):

```yaml
spec:
  replicas: 0
```

## Network and DNS

Egress, ingress, DNS policy, and DNS config are nested under `network`:

```yaml
spec:
  network:
    egress:
      - to:
          - ipBlock:
              cidr: 10.0.0.0/8
        ports:
          - protocol: TCP
            port: 443
    ingress:
      - from:
          - namespaceSelector:
              matchLabels:
                name: my-namespace
        ports:
          - protocol: TCP
            port: 8090
    dnsPolicy: None
    dnsConfig:
      nameservers:
        - 8.8.8.8
      searches:
        - my-domain.local
```

Disable the Service entirely:

```yaml
spec:
  enableService: false
```

## Annotations and Labels

Updating `annotations` or `labels` triggers a full pod rollout, even when no other configuration has changed:

```yaml
spec:
  annotations:
    custom-annotation: test-value
  labels:
    environment: production
    team: data-platform
```

Operator-reserved keys (e.g. `spice.ai/app`, `spice.ai/spicepod`, `spice.ai/version`, `spice.ai/cluster`, `spice.ai/cluster-role`, `spice.ai/cluster-mtls`, `spice.ai/component`, `spice.ai/observed-generation`, `spice.ai/rollback`, `spice.ai/sidecar-injected`, `spice.ai/validation-level`) are stripped from user-supplied annotations/labels.

## Health Probes

Probes are only created when the Spiced HTTP server is enabled (i.e. for non-executor nodes). Cluster executors have no probes.

```yaml
spec:
  probes:
    liveness:
      initialDelaySeconds: 10
      timeoutSeconds: 5
      periodSeconds: 30
      failureThreshold: 5
    readiness:
      initialDelaySeconds: 5
      periodSeconds: 10
```

## Pod Scheduling

```yaml
spec:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
        - matchExpressions:
            - key: kubernetes.io/arch
              operator: In
              values: [amd64]
  tolerations:
    - key: dedicated
      operator: Equal
      value: spice
      effect: NoSchedule
  terminationGracePeriodSeconds: 30
```

## Crashloop Protection

The operator monitors pods for repeated failures. When a `SpicepodSet` accumulates more dead pod observations than the threshold (default: `10`), the operator pauses the workload (`replicas → 0`) and records a `pauseReason` of `CrashLooping` in status. Configure via the operator CLI flag `--pause-crashlooping-pods-threshold` (`0` disables).

## Status

Useful fields on `status`:

| Field                             | Description                                                                                  |
| --------------------------------- | -------------------------------------------------------------------------------------------- |
| `replicas`                        | Formatted replica state for `kubectl` display, e.g. `2/5`.                                   |
| `readyReplicas` / `totalReplicas` | Numeric replica counts.                                                                      |
| `role`                            | `<none>`, `scheduler`, or `executor`.                                                        |
| `pauseReason`                     | Set when paused (e.g. `CrashLooping`).                                                       |
| `activeVersion`                   | Spec SHA of the version currently receiving traffic (instant rollback).                      |
| `standbyVersion`                  | Spec SHA of the standby retained for instant rollback.                                       |
| `standbyExpiresAt`                | Epoch seconds when the standby pods will be reclaimed.                                       |
| `conditions`                      | Standard Kubernetes `Condition`s describing reconciliation state.                            |

```bash
kubectl get spicepodset
# NAME          READY   ROLE     AGE   LAST UPDATED
# my-spicepod   3/3     <none>   2d    1m
```

## Monitoring

Telemetry properties passed to the Spice runtime are configured at the operator level (Helm values), not on the `SpicepodSet`:

```yaml
# Helm values for the operator
telemetryProperties:
  environment: production
  team: data-platform
  region: us-west-2
```
