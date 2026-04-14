---
description: List available Spice runtime versions
icon: box
---

# Container Images API

Container images are the Spice runtime versions available for deployments. Use this endpoint to discover available versions and select the appropriate image tag for your app.

## List Container Images

<mark style="color:blue;">`GET`</mark> `https://api.spice.ai/v1/container-images`

Returns available Spice runtime container images.

**Required scope:** `apps:read`

### Query Parameters

| Parameter | Type   | Default  | Description                               |
| --------- | ------ | -------- | ----------------------------------------- |
| `channel` | string | `stable` | Release channel: `stable` or `enterprise` |

### Response

{% tabs %}
{% tab title="200: OK" %}
```json
{
  "images": [
    {
      "name": "spiceai/spiceai:1.5.0-models",
      "tag": "1.5.0-models",
      "channel": "stable"
    },
    {
      "name": "spiceai/spiceai:1.4.0-models",
      "tag": "1.4.0-models",
      "channel": "stable"
    }
  ],
  "default": "1.5.0-models"
}
```

**Response Fields:**

| Field     | Type   | Description                                       |
| --------- | ------ | ------------------------------------------------- |
| `name`    | string | Full container image name with tag                |
| `tag`     | string | Image tag (use this in app config or deployments) |
| `channel` | string | Release channel (`stable` or `enterprise`)        |
| `default` | string | The default image tag for new apps                |
{% endtab %}

{% tab title="401: Unauthorized" %}
```json
{
  "error": "Missing Authorization header"
}
```
{% endtab %}
{% endtabs %}

### Examples

**cURL:**

```bash
curl -H "Authorization: Bearer <token>" \
  https://api.spice.ai/v1/container-images
```

**Filter by channel:**

```bash
curl -H "Authorization: Bearer <token>" \
  "https://api.spice.ai/v1/container-images?channel=enterprise"
```

**Python:**

```python
import requests

response = requests.get(
    "https://api.spice.ai/v1/container-images",
    headers={"Authorization": f"Bearer {token}"},
    params={"channel": "stable"}
)

data = response.json()
print(f"Default image: {data['default']}")

for image in data["images"]:
    print(f"  {image['tag']} ({image['channel']})")
```

**Node.js:**

```javascript
const response = await fetch('https://api.spice.ai/v1/container-images', {
  headers: {
    'Authorization': `Bearer ${token}`
  }
});

const data = await response.json();
console.log('Default image:', data.default);
console.log('Available images:', data.images.map(i => i.tag));
```

## Using Container Images

When creating or updating an app, use the `tag` field to specify which runtime version to use:

```bash
curl -X PUT https://api.spice.ai/v1/apps/123 \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "image_tag": "1.5.0-models"
  }'
```

**Terraform:**

```hcl
data "spiceai_container_images" "available" {
  channel = "stable"
}

resource "spiceai_app" "example" {
  name      = "my-app"
  image_tag = data.spiceai_container_images.available.default
  # ...
}
```

See also:

- [Apps API](apps.md) - Configure app runtime version
- [Deployments API](deployments.md) - Override image tag per deployment
- [Spice Runtime Versions](../../../portal/app-spicepod/spice-runtime-versions.md) - View versions in the portal
