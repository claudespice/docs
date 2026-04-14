---
description: Spice.ai Cloud Platform API Reference
icon: terminal
---

# API Reference

The Spice.ai Cloud Platform exposes two sets of APIs: **Runtime APIs** for querying data and AI, and the **Management API** for managing apps and infrastructure.

## Runtime APIs

Runtime APIs are served at `https://data.spiceai.io` and authenticated with [App API keys](../../portal/apps/api-keys.md).

| API                      | Endpoint                       | Documentation                                            |
| ------------------------ | ------------------------------ | -------------------------------------------------------- |
| SQL Query (HTTP)         | `POST /v1/sql`                 | [HTTP API](sql-query/http-api.md)                        |
| SQL Query (Arrow Flight) | `grpc+tls://flight.spiceai.io` | [Arrow Flight API](sql-query/apache-arrow-flight-api.md) |
| LLM Chat Completions     | `POST /v1/chat/completions`    | [LLM API](openai-api.md)                                 |
| Search                   | `POST /v1/search`              | [Search API](search.md)                                  |
| Health                   | `GET /health`                  | [Health API](health.md)                                  |
| Metrics                  | `GET /v1/metrics`              | [Metrics API](metrics.md)                                |

## Management API

The Management API is served at `https://api.spice.ai` and authenticated with [Personal Access Tokens](../../portal/profile/personal-access-tokens.md) or OAuth client credentials.

| Endpoint         | Documentation                                          |
| ---------------- | ------------------------------------------------------ |
| Apps             | [Apps API](management/apps.md)                         |
| Deployments      | [Deployments API](management/deployments.md)           |
| Secrets          | [Secrets API](management/secrets.md)                   |
| API Keys         | [API Keys API](management/api-keys.md)                 |
| Members          | [Members API](management/members.md)                   |
| Regions          | [Regions API](management/regions.md)                   |
| Container Images | [Container Images API](management/container-images.md) |
| Health           | [Health API](management/health.md)                     |

See the full [Management API reference](management/) for authentication, scopes, rate limits, and examples.

## OpenAPI Specification

The Spice.ai Platform API spec is available at:

```
https://api.spice.ai/v1/docs/openapi.json
```
