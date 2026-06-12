---
description: Create and manage apps on a dedicated, single-tenant cluster
icon: server
---

# Dedicated Clusters

Organizations on an enterprise plan can have one or more **dedicated clusters**: Spice-managed, single-tenant infrastructure where your apps run only alongside other apps from your organization — never on shared infrastructure. Each cluster has its own `cluster_name`, isolated network, and connection endpoint.

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
      "region": "us-west-2",
      "cloud_provider": "aws",
      "endpoint": "https://private-acme-prod-sandbox-us-west-2-prod-data.spiceai.io",
      "created_at": "2026-06-11T00:00:00Z",
      "updated_at": "2026-06-11T00:00:00Z"
    }
  ]
}
```

- **`cluster_name`** — the cluster's identifier. Use it when creating or reassigning apps.
- **`endpoint`** — the cluster's data-plane endpoint (an `https://` URL). Use it to connect to apps running on this cluster.

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

The response includes the resolved assignment and the cluster's endpoint:

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
If `region` is also provided it must match the cluster's region.
{% endhint %}

### Errors

| Status | Cause |
| ------ | ----- |
| `400` `Cluster '<name>' not found` | The cluster does not exist or is not registered to your organization |
| `400` `'<name>' is not a deployable cluster` | The name isn't a cluster you can deploy to — pass a `cluster_name` from `GET /v1/clusters` |
| `400` `region '<r>' does not match cluster region '<r2>'` | An explicit `region` was provided that differs from the cluster's region |

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

The app and `GET /v1/clusters` responses return the cluster's `endpoint` — the URL you connect to.

It serves the same APIs (SQL, search, and LLM over HTTP, plus Apache Arrow Flight), and authentication is unchanged — use the app's [API key](../../portal/apps/api-keys.md) or your platform credentials exactly as on shared infrastructure. For Apache Arrow Flight, take the endpoint's host, replace `-data` with `-flight`, and connect over `grpc+tls://<host>:443`.

When using the [SDKs](../../../sdks/), pass the endpoint in place of the `data.spiceai.io` / `flight.spiceai.io` defaults. For example with the [Python SDK](../../../sdks/python-sdk/), over Flight:

```python
from spicepy import Client

client = Client(
    api_key="<app-api-key>",
    url="grpc+tls://private-acme-prod-sandbox-us-west-2-prod-flight.spiceai.io:443",
)
```

Everything else — deployments, secrets, API keys, spicepod configuration — works identically to apps on shared infrastructure. See the [Management APIs](README.md) reference for the full endpoint documentation.
