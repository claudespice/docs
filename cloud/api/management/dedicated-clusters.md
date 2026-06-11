---
description: Create and manage apps on a dedicated, single-tenant cluster
icon: server
---

# Dedicated Clusters

Organizations on an enterprise plan can have one or more **dedicated clusters**: Spice-managed, single-tenant infrastructure reserved exclusively for the organization, with its own isolated network and dedicated query endpoints. Apps assigned to a dedicated cluster run only alongside other apps from your organization — never on shared infrastructure.

Dedicated clusters are provisioned by Spice.ai. Contact [support](https://spice.ai/support) to request one. Once provisioned and registered to your organization, it is available to the Management API and in the Portal's app-creation region picker.

## List your organization's clusters

`GET /v1/clusters` returns the dedicated clusters registered to the organization bound to your access token. Requires the `apps:read` scope.

```bash
curl -H "Authorization: Bearer <token>" \
  https://api.spice.ai/v1/clusters
```

```json
{
  "clusters": [
    {
      "cluster_name": "acme-prod",
      "region": "us-west-2",
      "cloud_provider": "aws",
      "endpoint": "acme-prod-us-west-2-prod-data.spiceai.io",
      "created_at": "2026-06-11T00:00:00Z",
      "updated_at": "2026-06-11T00:00:00Z"
    }
  ]
}
```

`cluster_name` is the cluster's identifier — use it when creating or reassigning apps. `endpoint` is the cluster's dedicated data-plane host (see [Querying apps on a dedicated cluster](#querying-apps-on-a-dedicated-cluster)).

## Create an app on a dedicated cluster

Pass `cluster_name` instead of `region` when creating an app. Provide exactly one of the two — the app's region is derived from the cluster. Requires the `apps:write` scope.

```bash
curl -X POST https://api.spice.ai/v1/apps \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "my-app",
    "cluster_name": "acme-prod",
    "description": "My app on our dedicated cluster"
  }'
```

The response includes the resolved cluster assignment:

```json
{
  "id": 123,
  "name": "my-app",
  "cluster_name": "acme-prod",
  "endpoint": "https://acme-prod-us-west-2-prod-data.spiceai.io",
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
| `400` `region '<r>' does not match cluster region '<r2>'` | An explicit `region` was provided that differs from the cluster's region |
| `400` `'cname' (deprecated) cannot be combined with 'cluster_name'` | Provide one region source only |

## Move an existing app to a dedicated cluster

`PATCH /v1/apps/{appId}` with `cluster_name` reassigns the app. Subsequent deployments land on the dedicated cluster, and the app's endpoints change to the cluster's dedicated hosts.

```bash
curl -X PATCH https://api.spice.ai/v1/apps/123 \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{"cluster_name": "acme-prod"}'
```

{% hint style="warning" %}
Reassigning an app changes its data and Flight endpoints. Update any clients that pin the shared regional hostnames, then create a new [deployment](README.md#create-a-deployment) so the app's runtime is placed on the cluster.
{% endhint %}

## Querying apps on a dedicated cluster

Apps on a dedicated cluster are served from the cluster's own endpoints rather than the shared regional ones:

| API | Endpoint |
| --- | -------- |
| HTTP (SQL, search, LLM) | `https://{cluster_name}-{region}-prod-data.spiceai.io` |
| Apache Arrow Flight | `grpc+tls://{cluster_name}-{region}-prod-flight.spiceai.io:443` |

The HTTP endpoint is returned as `endpoint` on the app; the Flight host is the same hostname with `-data` replaced by `-flight`. Authentication is unchanged — use the app's [API key](../../portal/apps/api-keys.md) or your platform credentials exactly as on shared infrastructure.

When using the [SDKs](../../../sdks/), pass the dedicated endpoints in place of the `data.spiceai.io` / `flight.spiceai.io` defaults. For example with the [Python SDK](../../../sdks/python-sdk/):

```python
from spicepy import Client

client = Client(
    api_key="<app-api-key>",
    url="grpc+tls://acme-prod-us-west-2-prod-flight.spiceai.io:443",
)
```

Everything else — deployments, secrets, API keys, spicepod configuration — works identically to apps on shared infrastructure. See the [Management APIs](README.md) reference for the full endpoint documentation.
