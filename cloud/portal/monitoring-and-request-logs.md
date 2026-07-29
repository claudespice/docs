---
description: >-
  Continuously identify and fix issues by tracking process actions with Spice
  monitoring and request logs.
icon: monitor-waveform
---

# Observability

The **Observability** tab provides real-time visibility into your app's usage, request performance, and API activity. Use it to track request volume, identify failures, and debug issues.

The tab groups the app's metrics, request logs, and traces into sections, listed in the left navigation:

* **Overview** — aggregate metrics for the app.
* **API** — API request metrics.
* **Cache** — results cache metrics. Shown when SQL results caching is enabled.
* **Data** — dataset and acceleration metrics.
* **Resources** — CPU and memory usage for the app's running instances.
* **Request Logs** — a record of individual API requests.
* **Traces** — task traces. Shown for apps with a spicepod.

## Accessing Observability

1. Navigate to your Spice app in the [portal](https://spice.ai).
2. Click the **Observability** tab in the app navigation bar.

## Overview

The **Overview** section shows aggregate metrics for:

* **SQL Queries** — total count, success/failure rate, and average duration.
* **AI Completions** — LLM inference request metrics.
* **Vector Searches** — embedding-based search request metrics.
* **Embedding Calculations** — embedding generation metrics.
* **Dataset Refreshes** — accelerated dataset refresh success and timing.

## Resources

The **Resources** section charts CPU and memory usage per running instance of the app.

1. Open the **Observability** tab for your app.
2. Select **Resources** in the left navigation.

CPU is reported as utilization against the app's configured CPU limit. Apps with no CPU limit set — including apps on dedicated clusters — report absolute core usage instead. Memory is reported in bytes.

Use the resource charts to size an app before raising its limits, and to correlate slow queries with memory or CPU pressure.

## Usage Metrics Dashboard

Track request volume, data usage, and query time across configurable time ranges.

1. Open the **Observability** tab for your app.
2. Select a time range: **1 hour**, **24 hours**, **7 days**, or **28 days**.
3. Review the dashboard charts for request counts, data transferred, and query latency.

Use the metrics dashboard to identify usage trends, detect spikes in query duration, and plan capacity.

## API Request Logs

The request logs provide a detailed record of individual API requests to your app's endpoints, including status codes, durations, and timestamps.

1. Open the **Observability** tab for your app.
2. Select **Request Logs** in the left navigation.
3. Select a time range: **past hour**, **8 hours**, **24 hours**, or **up to 3 days**.
4. Browse the log entries to inspect individual request details including endpoint, status code, and duration.

Use request logs to debug failing queries, identify slow requests, and audit API usage.
