# Limitations

{% hint style="info" %}
See plan-specific limits in [Pricing](../getting-started/pricing/)
{% endhint %}

Spice.ai has the following limitations:

#### Global API Limits

* 500 row limit for HTTP API results (use the [Apache Arrow API](../api/sql-query/apache-arrow-flight-api.md) or the [Async HTTP API](../api/sql-query/http-api-1.md) for no row limits).

#### Firecache Limits

* 60-second query timeout.

#### Data Limitations

* 128-bit integer limit (while data can be uint256). Both the 128-bit `DECIMAL(38)` type and `_hex` fields are included where the type is 256-bit.
