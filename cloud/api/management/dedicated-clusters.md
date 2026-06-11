---
description: Create and manage apps on a dedicated, single-tenant cluster
icon: server
---

# Dedicated Clusters

Organizations on an enterprise plan can have one or more **dedicated clusters**: Spice-managed, single-tenant infrastructure where your apps run only alongside other apps from your organization — never on shared public infrastructure. Each cluster has its own `cluster_name`, isolated network, and query endpoints.

Clusters can **share underlying infrastructure** with other clusters in your organization — for example an `acme-prod-sandbox` and an `acme-prod-staging` cluster running on the same hardware — or be **completely isolated** on their own dedicated infrastructure. Either way they are separate, independently-addressable clusters; the `infra_cluster_name` field tells you which clusters share infrastructure.

Dedicated clusters are provisioned by Spice.ai. Contact [support](https://spice.ai/support) to request one. Once provisioned and registered to your organization, your clusters are available to the Management API and in the Portal's app-creation picker.

## List your clusters

`GET /v1/clusters` returns the dedicated clusters registered to the organization bound to your access token. Requires the `apps:read` scope.

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

- **`cluster_name`** — the cluster's identifier. Use it when creating or reassigning apps.
- **`infra_cluster_name`** — the underlying infrastructure the cluster runs on. Clusters that share an `infra_cluster_name` run on the same hardware (while staying isolated from each other); a value unique to one cluster means it's fully isolated.
- **`endpoint`** — the cluster's **private** data-plane host (see [Querying apps](#querying-apps-on-a-dedicated-cluster)).

## Create an app on a dedicated cluster

Pass `cluster_name` instead of `region` when creating an app — set it to a `cluster_name` returned by `GET /v1/clusters`. Provide exactly one of the two; the app's region is derived from the cluster. Requires the `apps:write` scope.

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

The response includes the resolved assignment (`endpoint` is the cluster's private host):

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
If `region` is also provided it must match the cluster's region; the deprecated `cname` field cannot be combined with `cluster_name`.
{% endhint %}

### Errors

| Status | Cause |
| ------ | ----- |
| `400` `Cluster '<name>' not found` | The cluster does not exist or is not registered to your organization |
| `400` | The name you passed is shared infrastructure (an `infra_cluster_name`), not a deployable cluster — use a `cluster_name` from `GET /v1/clusters` |
| `400` `region '<r>' does not match cluster region '<r2>'` | An explicit `region` was provided that differs from the cluster's region |
| `400` `'cname' (deprecated) cannot be combined with 'cluster_name'` | Provide one region source only |

## Move an existing app to a dedicated cluster

`PUT /v1/apps/{appId}` with `cluster_name` reassigns the app. Subsequent deployments land on the cluster, and the app's endpoints change to the cluster's hosts.

```bash
curl -X PUT https://api.spice.ai/v1/apps/123 \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{"cluster_name": "acme-prod-sandbox"}'
```

{% hint style="warning" %}
Reassigning an app changes its data and Flight endpoints. Update any clients that pin the old hostnames, then create a new [deployment](README.md#create-a-deployment) so the app's runtime is placed on the cluster.
{% endhint %}

## Querying apps on a dedicated cluster

Apps on a dedicated cluster can be reached two ways — a **private** endpoint unique to the cluster, and a **public** endpoint on the cluster's underlying infrastructure. Both route to the same app; authentication is unchanged (use the app's [API key](../../portal/apps/api-keys.md) or your platform credentials exactly as on shared infrastructure).

| | HTTP (SQL, search, LLM) | Apache Arrow Flight | Availability |
| --- | --- | --- | --- |
| **Private** (this cluster) | `https://private-{cluster_name}-{region}-prod-data.spiceai.io` | `grpc+tls://private-{cluster_name}-{region}-prod-flight.spiceai.io:443` | Only when **VPC peering / private connectivity** is enabled — these hosts resolve to a private address inside the cluster's network |
| **Public** (shared infrastructure) | `https://{infra_cluster_name}-{region}-prod-data.spiceai.io` | `grpc+tls://{infra_cluster_name}-{region}-prod-flight.spiceai.io:443` | Always available over the public internet |

`GET /v1/clusters` and the app's `endpoint` field return the **private** host. If your cluster doesn't have private connectivity set up, use the **public** host instead — built from `infra_cluster_name` (the Flight host is the same hostname with `-data` replaced by `-flight`).

When using the [SDKs](../../../sdks/), pass the chosen endpoint in place of the `data.spiceai.io` / `flight.spiceai.io` defaults. For example with the [Python SDK](../../../sdks/python-sdk/), using the private Flight endpoint over VPC peering:

```python
from spicepy import Client

client = Client(
    api_key="<app-api-key>",
    url="grpc+tls://private-acme-prod-sandbox-us-west-2-prod-flight.spiceai.io:443",
)
```

Everything else — deployments, secrets, API keys, spicepod configuration — works identically to apps on shared infrastructure. See the [Management APIs](README.md) reference for the full endpoint documentation.
