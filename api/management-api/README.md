---
description: Management and Deployment API documentation for api.spice.ai
icon: code
---

# Management API

The Spice.ai Management API (`api.spice.ai`) provides programmatic access to manage your Spice.ai Cloud resources including apps, deployments, secrets, API keys, and organization members.

## Base URL

```
https://api.spice.ai
```

## API Version

All API endpoints are versioned under `/v1`:

```
https://api.spice.ai/v1
```

## Authentication

The Management API supports three authentication methods:

### 1. Personal Access Tokens (PATs)

Personal Access Tokens are user-scoped tokens that provide secure, long-lived access to the API. PATs are recommended for:
- CLI tools and automation scripts
- CI/CD pipelines
- Personal integrations

**Creating a PAT:**
1. Sign in to [Spice.ai Cloud Portal](https://spice.ai)
2. Navigate to **Profile** → **Personal Access Tokens**
3. Click **Create Token**
4. Select an organization and configure scopes
5. Copy the token (it won't be shown again)

**Using a PAT:**

```bash
curl -H "Authorization: Bearer <your-pat-token>" \
  https://api.spice.ai/v1/apps
```

Learn more: [Personal Access Tokens](../../portal/profile/personal-access-tokens.md)

### 2. OAuth 2.0 Client Credentials

OAuth client credentials are organization-scoped tokens ideal for:
- Service-to-service authentication
- Multi-tenant applications
- Third-party integrations

**Using OAuth:**

```bash
# Exchange client credentials for an access token
curl -X POST https://spice.ai/api/oauth/token \
  -H "Content-Type: application/json" \
  -d '{
    "client_id": "your-client-id",
    "client_secret": "your-client-secret",
    "grant_type": "client_credentials"
  }'

# Use the access token
curl -H "Authorization: Bearer <access-token>" \
  https://api.spice.ai/v1/apps
```

### 3. User Session Tokens (CLI)

The Spice CLI uses user session tokens obtained through the browser-based login flow. These tokens provide full access to resources in your personal organization.

```bash
spice login  # Initiates browser-based authentication
```

## OAuth Scopes

Access to API resources is controlled through scopes. PATs and OAuth clients must be granted appropriate scopes:

| Scope               | Description                                                   |
| ------------------- | ------------------------------------------------------------- |
| `*`                 | Full access to all resources (not recommended for production) |
| `apps:read`         | Read app information                                          |
| `apps:write`        | Create and update apps                                        |
| `apps:delete`       | Delete apps                                                   |
| `deployments:read`  | View deployment status and history                            |
| `deployments:write` | Create new deployments                                        |
| `secrets:read`      | List and view secrets (values are masked)                     |
| `secrets:write`     | Create, update, and delete secrets                            |
| `config:read`       | Read app configuration                                        |
| `config:write`      | Update app configuration                                      |
| `members:read`      | View organization members                                     |
| `members:write`     | Add and update organization members                           |
| `members:delete`    | Remove organization members                                   |

**Scope Hierarchy:**
- Write scopes (`apps:write`) imply read access (`apps:read`)
- Wildcard scope (`*`) grants all permissions

## Rate Limiting

The API implements rate limiting to ensure service stability:
- **Per-user:** 1000 requests per minute
- **Per-organization:** 10,000 requests per minute

Rate limit information is included in response headers:
```
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 995
X-RateLimit-Reset: 1640000000
```

## Error Responses

The API uses standard HTTP status codes:

| Status Code                 | Description                             |
| --------------------------- | --------------------------------------- |
| `200 OK`                    | Request succeeded                       |
| `201 Created`               | Resource created successfully           |
| `202 Accepted`              | Request accepted (async operation)      |
| `204 No Content`            | Request succeeded with no response body |
| `400 Bad Request`           | Invalid request body or parameters      |
| `401 Unauthorized`          | Missing or invalid authentication       |
| `403 Forbidden`             | Insufficient scope or permissions       |
| `404 Not Found`             | Resource not found                      |
| `409 Conflict`              | Resource already exists or conflict     |
| `429 Too Many Requests`     | Rate limit exceeded                     |
| `500 Internal Server Error` | Server error                            |

**Error Response Format:**

```json
{
  "error": "Human-readable error message",
  "details": {
    "fieldErrors": {
      "field_name": ["Validation error message"]
    }
  }
}
```

## Pagination

List endpoints support pagination through query parameters:

| Parameter | Type    | Default | Description                                  |
| --------- | ------- | ------- | -------------------------------------------- |
| `limit`   | integer | 20      | Maximum number of items to return (max: 100) |
| `offset`  | integer | 0       | Number of items to skip                      |

## OpenAPI Specification

Interactive API documentation is available at:

```
https://api.spice.ai/v1/docs
```

Download the OpenAPI specification:

```
https://api.spice.ai/v1/docs/openapi.json
```

## SDK Support

Official SDKs are available for popular languages:
- [Python SDK](../../sdks/python-sdk/)
- [Node.js SDK](../../sdks/node.js-sdk/)
- [Go SDK](../../sdks/go.md)
- [Rust SDK](../../sdks/rust-sdk/)

## Endpoints

- [Health](health.md) - API health check
- [Regions](regions.md) - List available deployment regions
- [Apps](apps.md) - Manage Spice apps
- [Deployments](deployments.md) - Deploy and manage app deployments
- [Secrets](secrets.md) - Manage app secrets
- [API Keys](api-keys.md) - Manage app API keys
- [Members](members.md) - Manage organization members
- [Container Images](container-images.md) - List available runtime versions

## Terraform Provider

The Management API supports infrastructure-as-code workflows through the [Spice.ai Terraform Provider](terraform.md). See the [Terraform Provider](terraform.md) page for full documentation including resources, data sources, import, and complete examples.

## Examples

### List all apps

```bash
curl -H "Authorization: Bearer <token>" \
  https://api.spice.ai/v1/apps
```

### Create a new app

```bash
curl -X POST https://api.spice.ai/v1/apps \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "my-app",
    "cname": "us-east-2.spice.cloud",
    "description": "My Spice app",
    "visibility": "private"
  }'
```

### Create a deployment

```bash
curl -X POST https://api.spice.ai/v1/apps/123/deployments \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "branch": "main",
    "commit_sha": "abc123",
    "commit_message": "Deploy latest changes"
  }'
```

### Add a secret

```bash
curl -X POST https://api.spice.ai/v1/apps/123/secrets \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "DATABASE_URL",
    "value": "postgresql://user:pass@host:5432/db"
  }'
```

## Support

For questions or issues with the Management API:
- [GitHub Issues](https://github.com/spicehq/spiceai/issues)
- [Community Discord](https://discord.gg/spiceai)
- [Support](../../support/support.md)
