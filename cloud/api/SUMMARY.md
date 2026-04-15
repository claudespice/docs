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

* [Management APIs](management/README.md)
  * ```yaml
    type: builtin:openapi
    props:
      models: true
      downloadLink: true
    dependencies:
      spec:
        ref:
          kind: openapi
          spec: spiceai-management-api
    ```
* [Terraform Provider](management-api/terraform.md)
