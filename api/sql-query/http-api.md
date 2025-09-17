---
description: Query web3 data with SQL via the HTTP API
---

# HTTP API

Blockchain and contract data may be queried by posting SQL to the `/v1/sql` API and `/v1/firesql` API for Firecached data. For documentation on the Spice Firecache see [Broken link](broken-reference "mention").

See [Tables](broken-reference) for a list of tables to query or browse the example queries listed in the menu.

#### Requirements and limitations

* An API key is required for all SQL queries.
* Results are limited to 500 rows. Use the [Apache Arrow Flight API](apache-arrow-flight-api.md) to fetch up to 1M rows in a single query or the [Async HTTP API](broken-reference) to fetch results with paging.
* Requests are limited to 90 seconds.

{% openapi-operation spec="spiceai-platform-api" path="/v1/sql" method="post" %}
[OpenAPI spiceai-platform-api](https://gitbook-x-prod-openapi.4401d86825a13bf607936cc3a9f3897a.r2.cloudflarestorage.com/raw/c89c6bd89b0927536006e57e1e8b60a228d4516831a94430f33c5b3c0bf155f4.yaml?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=dce48141f43c0191a2ad043a6888781c%2F20250917%2Fauto%2Fs3%2Faws4_request&X-Amz-Date=20250917T031032Z&X-Amz-Expires=172800&X-Amz-Signature=594c533d49b28df2b1e3778a9c6a75714053a5adb07c24d0525a15676ef7a2db&X-Amz-SignedHeaders=host&x-amz-checksum-mode=ENABLED&x-id=GetObject)
{% endopenapi-operation %}
