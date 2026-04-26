---
description: Metrics, logs, traces, and alerts for production Spice.ai Enterprise deployments.
icon: chart-line
---

# Observability

Spice.ai Enterprise exposes a comprehensive set of metrics, structured logs, and OpenTelemetry traces. This page documents how to wire each into a production observability stack and which signals to alert on.

## Metrics

The runtime exposes Prometheus metrics on port `9090` at `/metrics`. The Helm chart and the Kubernetes operator both ship with first-class scrape integration:

- **Helm chart**: set `monitoring.podMonitor.enabled: true` to deploy a `PodMonitor`.
- **Operator**: set `servicemonitor.enabled: true` to deploy a `ServiceMonitor` for the operator itself.

```yaml
# values.yaml (Spice Helm chart)
monitoring:
  podMonitor:
    enabled: true
    additionalLabels:
      release: prometheus
```

### Key metrics

| Metric                                       | Type      | Meaning                                                                              |
| -------------------------------------------- | --------- | ------------------------------------------------------------------------------------ |
| `spiced_query_duration_seconds`              | Histogram | End-to-end query latency.                                                            |
| `spiced_query_total{result="error"}`         | Counter   | Failed queries. Drives the [error rate alert](#alerts).                              |
| `spiced_acceleration_refresh_duration_seconds` | Histogram | Per-dataset acceleration refresh latency.                                          |
| `spiced_acceleration_refresh_total{result}`  | Counter   | Refresh successes and failures per dataset.                                          |
| `spiced_acceleration_rows`                   | Gauge     | Row count per accelerated dataset.                                                   |
| `spiced_http_request_duration_seconds`       | Histogram | HTTP API latency by route.                                                           |
| `spiced_flight_request_duration_seconds`     | Histogram | Arrow Flight RPC latency.                                                            |
| `spiced_cluster_executor_count`              | Gauge     | (`SpicepodCluster`) Number of executors registered with the scheduler.               |
| `spiced_cluster_certificate_expiry_seconds`  | Gauge     | (`SpicepodCluster`) Time-to-expiry for the per-node leaf certificate.                |
| `spiceai_operator_reconcile_duration_seconds`| Histogram | Operator reconcile loop latency.                                                     |
| `spiceai_operator_pod_dead_total`            | Counter   | Dead pod observations. Triggers crashloop pause when above the configured threshold. |

For the full list, query the running runtime: `curl localhost:9090/metrics | grep -E '^# HELP'`.

## Grafana dashboard

Spice.ai publishes a maintained Grafana dashboard with the panels operations teams need most often (query rate / latency / errors, acceleration freshness and row counts, executor registration, certificate expiry).

Import via dashboard ID or copy the JSON from the [Spice.ai Grafana dashboard](https://spiceai.org/docs/monitoring/grafana). The dashboard is compatible with Prometheus, Amazon Managed Prometheus, Azure Managed Prometheus, and Google Cloud Managed Service for Prometheus.

## Logs

Spice emits structured JSON logs on stdout. The log level is controlled by `SPICED_LOG`:

| Level   | Use                                                                |
| ------- | ------------------------------------------------------------------ |
| `ERROR` | Production default.                                                |
| `WARN`  | Production with elevated visibility.                               |
| `INFO`  | Default during cutover and incident investigation.                 |
| `DEBUG` | Development and targeted debugging only \u2014 not for production. |

```yaml
spec:
  env:
    - name: SPICED_LOG
      value: INFO
```

### Log routing

| Destination                  | Recommended forwarder                                                       |
| ---------------------------- | --------------------------------------------------------------------------- |
| **CloudWatch Logs**          | Fluent Bit DaemonSet with the `cloudwatch_logs` output plugin.              |
| **Azure Monitor**            | [Container insights](https://learn.microsoft.com/azure/azure-monitor/containers/container-insights-overview). |
| **Google Cloud Logging**     | The GKE-managed logging agent (default on Autopilot / Standard).            |
| **Datadog**                  | Datadog Agent with the Kubernetes integration enabled.                      |
| **Elastic / OpenSearch**     | Filebeat or Fluent Bit with the `elasticsearch` / `opensearch` output.       |
| **Loki**                     | [Grafana Alloy](https://grafana.com/docs/alloy/) or Promtail.                |

Always retain query-error and acceleration-refresh-failure log lines for at least 30 days for incident review.

## Distributed tracing

When the runtime is started with `--otel-endpoint` or the `SPICED_OTEL_ENDPOINT` environment variable, Spice exports OpenTelemetry traces over OTLP/gRPC. Traces cover query parsing, optimization, and execution; for `SpicepodCluster`, scheduler-to-executor RPCs are linked into a single trace.

```yaml
spec:
  env:
    - name: SPICED_OTEL_ENDPOINT
      value: http://otel-collector.observability:4317
    - name: SPICED_OTEL_RESOURCE_ATTRIBUTES
      value: service.name=spiceai,deployment.environment=prod
```

Pair with an OpenTelemetry Collector configured for the organization's tracing backend (Tempo, Jaeger, Datadog, Honeycomb, X-Ray).

## Alerts

The following alerts are the minimum recommended set for production. Tune thresholds against the deployment's observed baseline.

```yaml
groups:
  - name: spiceai
    interval: 30s
    rules:
      - alert: SpiceAIQueryErrorRateHigh
        expr: |
          sum(rate(spiced_query_total{result="error"}[5m]))
            /
          sum(rate(spiced_query_total[5m])) > 0.05
        for: 10m
        labels:
          severity: warning
        annotations:
          summary: "Query error rate above 5% for 10 minutes."

      - alert: SpiceAIAccelerationRefreshFailing
        expr: |
          increase(spiced_acceleration_refresh_total{result="error"}[15m]) > 3
        for: 15m
        labels:
          severity: warning
        annotations:
          summary: "Acceleration refresh failing for {{ $labels.dataset }}."

      - alert: SpiceAIQueryLatencyP95
        expr: |
          histogram_quantile(0.95, sum by (le) (
            rate(spiced_query_duration_seconds_bucket[5m])
          )) > 2
        for: 10m
        labels:
          severity: warning
        annotations:
          summary: "p95 query latency above 2s."

      - alert: SpiceAIExecutorMissing
        expr: |
          spiced_cluster_executor_count < 2
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "{{ $labels.cluster }} has fewer than 2 executors registered."

      - alert: SpiceAICertificateExpiringSoon
        expr: |
          spiced_cluster_certificate_expiry_seconds < 7 * 24 * 3600
        for: 10m
        labels:
          severity: warning
        annotations:
          summary: "Cluster certificate expires in less than 7 days."

      - alert: SpiceAICrashLoop
        expr: |
          increase(spiceai_operator_pod_dead_total[15m]) > 5
        for: 15m
        labels:
          severity: critical
        annotations:
          summary: "{{ $labels.spicepodset }} crashlooping \u2014 operator may pause it."
```

## Health endpoints

| Endpoint            | Purpose                                                                                        |
| ------------------- | ---------------------------------------------------------------------------------------------- |
| `GET /health`       | Liveness. Returns `200 OK` when the process is responsive. Use for container liveness probes.   |
| `GET /v1/ready`     | Readiness. Returns `200 OK` only once datasets and accelerations have completed initial load. Use for container readiness probes and load balancer health checks. |
| `GET /metrics`      | Prometheus metrics on port `9090`. Should never be exposed externally.                          |

The Spice Helm chart and the operator configure liveness and readiness probes against these endpoints by default. Tune the probe parameters on heavy initial-load workloads via [`probes`](../kubernetes/spicepodset.md):

```yaml
spec:
  probes:
    readiness:
      initialDelaySeconds: 30
      periodSeconds: 10
      failureThreshold: 6
    liveness:
      initialDelaySeconds: 60
      periodSeconds: 30
      failureThreshold: 5
```
