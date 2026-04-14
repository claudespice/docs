# Table of contents

* [API Reference](README.md)

## Runtime APIs

* [SQL Query API](sql-query/README.md)
  * [HTTP API](sql-query/http-api.md)
  * [Apache Arrow Flight API](sql-query/apache-arrow-flight-api.md)
* [LLM API](openai-api.md)
* [Search API](search.md)
* [Health API](health.md)
* [Metrics API](metrics.md)

## Management API

* [Management API](management/README.md)
  * [Health](management/health.md)
  * [Regions](management/regions.md)
  * [Apps](management/apps.md)
  * [Deployments](management/deployments.md)
  * [Secrets](management/secrets.md)
  * [API Keys](management/api-keys.md)
  * [Members](management/members.md)
  * [Container Images](management/container-images.md)
  * [Terraform Provider](management/terraform.md)

## Reference

* ```yaml
  props:
    models: false
  type: builtin:openapi
  dependencies:
    spec:
      ref:
        kind: openapi
        spec: spiceai-platform-api
  ```
