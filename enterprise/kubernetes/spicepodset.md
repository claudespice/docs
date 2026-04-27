---
description: SpicepodSet CRD reference for deploying and managing Spicepod replicas.
icon: layer-group
---

# SpicepodSet

A `SpicepodSet` deploys and manages one or more Spicepod replicas. The operator handles the full lifecycle: creating the workload, rolling updates, volume management, health monitoring, and crashloop protection.

## Minimal Example

```yaml
apiVersion: spice.ai/v1
kind: SpicepodSet
metadata:
  name: my-spicepod
  namespace: default
spec:
  replicas: 1
  spicepod: |
    name: my-spicepod
    kind: Spicepod
    version: v1
```

## Container Image

Configure a custom Spice.ai Enterprise image:

```yaml
spec:
  spiceai_image_registry: 709825985650.dkr.ecr.us-east-1.amazonaws.com
  spiceai_image_name: spice-ai/spiceai-enterprise-byol
  spiceai_image_tag: latest-models
  spiceai_image_pull_secret: my-pull-secret
```

## Ports

```yaml
spec:
  spiced:
    http_port: 8090
    flight_port: 50051
    metrics_port: 9090
```

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
      value: "debug"
    - name: MY_SECRET
      valueFrom:
        secretKeyRef:
          name: my-secret
          key: api-key
  env_from_source:
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
    storage_class_name: standard
    storage_requests: 10Gi
```

{% hint style="warning" %}
Volume shrinking is not supported. Decreasing `storage_requests` has no effect on existing PVCs.
{% endhint %}

When volumes are configured, the operator creates per-replica `StatefulSet`s with `PersistentVolumeClaim`s. Increasing `storage_requests` triggers automatic PVC resizing.

## Service Account

### Operator-Managed

```yaml
spec:
  service_account:
    enabled: true
    create: true
```

### Bring Your Own

```yaml
spec:
  service_account:
    enabled: true
    create: false
    name: my-existing-service-account
```

### With IRSA Annotations

```yaml
spec:
  service_account:
    enabled: true
    create: true
    annotations:
      eks.amazonaws.com/role-arn: "arn:aws:iam::123456789012:role/spice-ai-role"
```

{% hint style="info" %}
To annotate only the ServiceAccount (e.g. for IRSA), use `service_account.annotations` instead of `spec.annotations`.
{% endhint %}

## Update Strategies

| Strategy                      | Behavior                                                                              |
| ----------------------------- | ------------------------------------------------------------------------------------- |
| **RollingParallel** (default) | Updates pods in parallel, keeping at least one available. Supports `max_unavailable`. |
| **RollingOrdered**            | Updates pods one at a time in ordinal order, waiting for each to become ready.        |
| **Parallel**                  | Updates all pods simultaneously with no availability constraints.                     |

```yaml
# RollingParallel (default)
spec:
  replicas: 5
  update_strategy:
    type: RollingParallel
    max_unavailable: 2

# RollingOrdered
spec:
  update_strategy:
    type: RollingOrdered

# Parallel
spec:
  update_strategy:
    type: Parallel
```

During rolling updates, new and unready replicas are updated first. Existing healthy replicas are only removed once replacements are ready.

## Scaling and Pausing

Set `replicas: 0` to scale down to zero pods while retaining supporting resources (Service, ConfigMap, NetworkPolicy, ServiceAccount):

```yaml
spec:
  replicas: 0
```

## Network Policy

Configure egress and ingress rules:

```yaml
spec:
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
```

Disable the Service:

```yaml
spec:
  enable_service: false
```

## DNS Configuration

Override pod-level DNS:

```yaml
spec:
  dnsPolicy: None
  dnsConfig:
    nameservers:
      - 8.8.8.8
    searches:
      - my-domain.local
```

## Annotations and Labels

Updating `annotations` or `labels` triggers a full pod rollout, even when no other configuration has changed:

```yaml
spec:
  annotations:
    custom-annotation: "test-value"
  labels:
    environment: production
    team: data-platform
```

## Health Probes

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
              values:
                - amd64
  tolerations:
    - key: "dedicated"
      operator: "Equal"
      value: "spice"
      effect: "NoSchedule"
  terminationGracePeriodSeconds: 30
```

## Crashloop Protection

The operator monitors pods for repeated failures. When a `SpicepodSet` accumulates more dead pod observations than the configured threshold (default: 10), the operator automatically pauses the workload by setting replicas to 0.

Configure the threshold via the operator CLI flag `--pause-crashlooping-pods-threshold`.

## Monitoring

Enable telemetry properties passed to the Spice runtime:

```yaml
# Helm values for the operator
telemetryProperties:
  environment: production
  team: data-platform
  region: us-west-2
```
