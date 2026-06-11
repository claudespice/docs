---
description: Create and manage apps on a dedicated, single-tenant cluster
icon: server
---

# Dedicated Clusters

Organizations on an enterprise plan can have one or more **dedicated clusters**: Spice-managed, single-tenant infrastructure reserved exclusively for the organization, with its own isolated network and dedicated query endpoints. Apps assigned to a dedicated cluster run only alongside other apps from your organization — never on shared infrastructure.

A dedicated cluster exposes one or more **deployment targets**. Each target has its own `cluster_name` and its own private endpoint, so you can keep separate environments (for example `acme-prod-sandbox` and `acme-prod-staging`) isolated on the same dedicated hardware. You assign an app to a target by its `cluster_name`.

Dedicated clusters are provisioned by Spice.ai. Contact [support](https://spice.ai/support) to request one. Once provisioned and registered to your organization, the deployment targets are available to the Management API and in the Portal's app-creation picker.

## List your deployment targets

`GET /v1/clusters` returns the deployment targets registered to the organization bound to your access token. Requires the `apps:read` scope.

```bash
curl -H "Authorization: Bearer <token>" \
  https://api.spice.ai/v1/clusters
```

```json
{
  "clusters": [
    {
      "cluster_name": "acme-prod-sandbox",
      "infra_cluster_name": "acme-prod",
      "region": "us-west-2",
      "cloud_provider": "aws",
      "endpoint": "private-acme-prod-sandbox-us-west-2-prod-data.spiceai.io",
      "created_at": "2026-06-11T00:00:00Z",
      "updated_at": "2026-06-11T00:00:00Z"
    },
    {
      "cluster_name": "acme-prod-staging",
      "infra_cluster_name": "acme-prod",
      "region": "us-west-2",
      "cloud_provider": "aws",
      "endpoint": "private-acme-prod-staging-us-west-2-prod-data.spiceai.io",
      "created_at": "2026-06-11T00:00:00Z",
      "updated_at": "2026-06-11T00:00:00Z"
    }
  ]
}
```

- **`cluster_name`** — the deployment target's identifier. Use it when creating or reassigning apps.
- **`infra_cluster_name`** — the dedicated cluster the target runs on (read-only). Targets that share an `infra_cluster_name` are isolated environments on the same cluster.
- **`endpoint`** — the target's **private** data-plane host (see [Querying apps](#querying-apps-on-a-dedicated-cluster)).

## Create an app on a deployment target

Pass `cluster_name` instead of `region` when creating an app — set it to a `cluster_name` returned by `GET /v1/clusters`. Provide exactly one of the two; the app's region is derived from the target. Requires the `apps:write` scope.

```bash
curl -X POST https://api.spice.ai/v1/apps \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "my-app",
    "cluster_name": "acme-prod-sandbox",
    "description": "My app on our dedicated cluster"
  }'
```

The response includes the resolved assignment (`endpoint` is the target's private host):

```json
{
  "id": 123,
  "name": "my-app",
  "cluster_name": "acme-prod-sandbox",
  "endpoint": "https://private-acme-prod-sandbox-us-west-2-prod-data.spiceai.io",
  "...": "..."
}
```

Apps created without `cluster_name` deploy to the shared regional infrastructure as usual; `cluster_name: null` is equivalent to omitting it.

{% hint style="info" %}
If `region` is also provided it must match the target's region; the deprecated `cname` field cannot be combined with `cluster_name`.
{% endhint %}

### Errors

| Status | Cause |
| ------ | ----- |
| `400` `Cluster '<name>' not found` | The target does not exist or is not registered to your organization |
| `400` `'<name>' is a cluster, not a deployment target; assign one of its nodegroups` | You passed an `infra_cluster_name`; assign a deployment-target `cluster_name` from `GET /v1/clusters` |
| `400` `region '<r>' does not match cluster region '<r2>'` | An explicit `region` was provided that differs from the target's region |
| `400` `'cname' (deprecated) cannot be combined with 'cluster_name'` | Provide one region source only |

## Move an existing app to a deployment target

`PUT /v1/apps/{appId}` with `cluster_name` reassigns the app. Subsequent deployments land on the target, and the app's endpoints change to the target's hosts.

```bash
curl -X PUT https://api.spice.ai/v1/apps/123 \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{"cluster_name": "acme-prod-sandbox"}'
```

{% hint style="warning" %}
Reassigning an app changes its data and Flight endpoints. Update any clients that pin the old hostnames, then create a new [deployment](README.md#create-a-deployment) so the app's runtime is placed on the target.
{% endhint %}

## Querying apps on a dedicated cluster

Apps on a dedicated cluster can be reached two ways — a **private** per-target endpoint and a **public** per-cluster endpoint. Both route to the same app; authentication is unchanged (use the app's [API key](../../portal/apps/api-keys.md) or your platform credentials exactly as on shared infrastructure).

| | HTTP (SQL, search, LLM) | Apache Arrow Flight | Availability |
| --- | --- | --- | --- |
| **Private** (per target) | `https://private-{cluster_name}-{region}-prod-data.spiceai.io` | `grpc+tls://private-{cluster_name}-{region}-prod-flight.spiceai.io:443` | Only when **VPC peering / private connectivity** is enabled for the cluster — these hosts resolve to a private address inside the cluster's network |
| **Public** (per cluster) | `https://{infra_cluster_name}-{region}-prod-data.spiceai.io` | `grpc+tls://{infra_cluster_name}-{region}-prod-flight.spiceai.io:443` | Always available over the public internet |

`GET /v1/clusters` and the app's `endpoint` field return the **private** host. If your cluster doesn't have private connectivity set up, use the **public** host instead — derive it from `infra_cluster_name` (the Flight host is the same hostname with `-data` replaced by `-flight`).

When using the [SDKs](../../../sdks/), pass the chosen endpoint in place of the `data.spiceai.io` / `flight.spiceai.io` defaults. For example with the [Python SDK](../../../sdks/python-sdk/), using the private Flight endpoint over VPC peering:

```python
from spicepy import Client

client = Client(
    api_key="<app-api-key>",
    url="grpc+tls://private-acme-prod-sandbox-us-west-2-prod-flight.spiceai.io:443",
)
```

Everything else — deployments, secrets, API keys, spicepod configuration — works identically to apps on shared infrastructure. See the [Management APIs](README.md) reference for the full endpoint documentation.
