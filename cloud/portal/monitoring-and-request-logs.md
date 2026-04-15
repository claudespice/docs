---
description: >-
  Continuously identify and fix issues by tracking process actions with Spice
  monitoring and request logs.
icon: monitor-waveform
---

# Monitoring

The **Monitoring** tab provides real-time visibility into your app's usage, request performance, and API activity. Use it to track request volume, identify failures, and debug issues.

## Accessing Monitoring

1. Navigate to your Spice app in the [portal](https://spice.ai).
2. Click the **Monitoring** tab in the app navigation sidebar.

## Overview

The Monitoring overview shows aggregate metrics for:

* **SQL Queries** — total count, success/failure rate, and average duration.
* **AI Completions** — LLM inference request metrics.
* **Vector Searches** — embedding-based search request metrics.
* **Embedding Calculations** — embedding generation metrics.
* **Dataset Refreshes** — accelerated dataset refresh success and timing.

## Usage Metrics Dashboard

Track request volume, data usage, and query time across configurable time ranges.

1. Open the **Monitoring** tab for your app.
2. Select a time range: **1 hour**, **24 hours**, **7 days**, or **28 days**.
3. Review the dashboard charts for request counts, data transferred, and query latency.

Use the metrics dashboard to identify usage trends, detect spikes in query duration, and plan capacity.

## API Request Logs

The request logs provide a detailed record of individual API requests to your app's endpoints, including status codes, durations, and timestamps.

1. Open the **Monitoring** tab for your app.
2. Click **Logs** to switch from the Metrics view to the Logs view.
3. Select a time range: **past hour**, **8 hours**, **24 hours**, or **up to 3 days**.
4. Browse the log entries to inspect individual request details including endpoint, status code, and duration.

Use request logs to debug failing queries, identify slow requests, and audit API usage.
