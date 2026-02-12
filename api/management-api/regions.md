---
description: List available deployment regions
icon: globe
---

# Regions API

Regions define where your Spice apps are deployed. Each region is operated by a cloud provider (AWS, Azure, or Teraswitch).

## List Regions

<mark style="color:blue;">`GET`</mark> `https://api.spice.ai/v1/regions`

Returns a list of available regions where apps can be deployed.

**Required scope:** `apps:read`

### Response

{% tabs %}
{% tab title="200: OK" %}
```json
{
  "regions": [
    {
      "name": "US East (Ohio)",
      "region": "us-east-2",
      "cname": "us-east-2",
      "provider": "aws",
      "providerName": "AWS"
    },
    {
      "name": "US West (Oregon)",
      "region": "us-west-2",
      "cname": "us-west-2",
      "provider": "aws",
      "providerName": "AWS"
    }
  ],
  "default": "us-east-2"
}
```

**Response Fields:**

| Field          | Type   | Description                                   |
| -------------- | ------ | --------------------------------------------- |
| `name`         | string | Human-readable region name                    |
| `region`       | string | Region identifier                             |
| `cname`        | string | Region identifier used when creating apps     |
| `provider`     | string | Cloud provider (`aws`, `azure`, `teraswitch`) |
| `providerName` | string | Human-readable provider name                  |
| `default`      | string | The default region identifier                 |
{% endtab %}

{% tab title="401: Unauthorized" %}
```json
{
  "error": "Missing Authorization header"
}
```
{% endtab %}

{% tab title="403: Forbidden" %}
```json
{
  "error": "Insufficient scope. Required: apps:read"
}
```
{% endtab %}
{% endtabs %}

### Examples

**cURL:**

```bash
curl -H "Authorization: Bearer <token>" \
  https://api.spice.ai/v1/regions
```

**Response:**

```json
{
  "regions": [
    {
      "name": "US East (Ohio)",
      "region": "us-east-2",
      "cname": "us-east-2",
      "provider": "aws",
      "providerName": "AWS"
    },
    {
      "name": "US West (Oregon)",
      "region": "us-west-2",
      "cname": "us-west-2",
      "provider": "aws",
      "providerName": "AWS"
    },
    {
      "name": "Europe (Frankfurt)",
      "region": "eu-central-1",
      "cname": "eu-central-1",
      "provider": "aws",
      "providerName": "AWS"
    }
  ],
  "default": "us-east-2"
}
```

**Python:**

```python
import requests

response = requests.get(
    "https://api.spice.ai/v1/regions",
    headers={"Authorization": f"Bearer {token}"}
)

regions = response.json()["regions"]
for region in regions:
    print(f"{region['name']} ({region['region']})")
```

**Node.js:**

```javascript
const response = await fetch('https://api.spice.ai/v1/regions', {
  headers: {
    'Authorization': `Bearer ${token}`
  }
});

const data = await response.json();
console.log('Available regions:', data.regions);
```

## Using Regions

When creating an app, use the `cname` field from the regions response:

```bash
curl -X POST https://api.spice.ai/v1/apps \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "my-app",
    "cname": "us-east-2"
  }'
```

## Region Selection

Consider these factors when selecting a region:

- **Latency:** Choose a region close to your users or data sources
- **Compliance:** Some regions may be required for data residency requirements
- **Availability:** Check region availability for your plan tier

{% hint style="info" %}
The default region (`us-east-2`) is recommended for most use cases.
{% endhint %}
