---
description: Query web3 data with SQL via the HTTP API
---

# HTTP API

Blockchain and contract data may be queried by posting SQL to the `/v1/sql` API and `/v1/firesql` API for Firecached data. For documentation on the Spice Firecache see [Broken link](broken-reference "mention").

See [Tables](broken-reference) for a list of tables to query or browse the example queries listed in the menu.

#### Requirements and limitations

* An API key is required for all SQL queries.
* Results are limited to 500 rows. Use the [Apache Arrow Flight API](apache-arrow-flight-api.md) to fetch up to 1M rows in a single query or the [Async HTTP API](http-api-1.md) to fetch results with paging.
* Requests are limited to 90 seconds.

## Perform a SQL query

<mark style="color:green;">`POST`</mark> `https://data.spiceai.io/v1/sql`

The SQL query should be sent in the body of the request as plain text

#### Query Parameters

| Name     | Type   | Description                    |
| -------- | ------ | ------------------------------ |
| api\_key | String | The API Key for your Spice app |

#### Headers

| Name                                           | Type   | Description                    |
| ---------------------------------------------- | ------ | ------------------------------ |
| Content-Type<mark style="color:red;">\*</mark> | String | text/plain                     |
| X-API-KEY                                      | String | The API Key for your Spice app |

{% tabs %}
{% tab title="200: OK Query result" %}
```javascript
{
    // Example response from: `select count(number) from eth.recent_blocks`
    {
    "rowCount": 1,
    "schema": [
        {
            "name": "EXPR$0",
            "type": {
                "name": "BIGINT"
            }
        }
    ],
    "rows": [
        {
            "EXPR$0": 149
        }
    ]
}
}
```
{% endtab %}

{% tab title="400: Bad Request The query could not be parsed" %}
```javascript
{
    // Response
}
```
{% endtab %}

{% tab title="401: Unauthorized Missing the API Key" %}
```javascript
{
    // Response
}
```
{% endtab %}
{% endtabs %}

## Perform a Firecache SQL Query

<mark style="color:green;">`POST`</mark> `https://data.spiceai.io/v1/firesql`

The SQL query should be sent in the body of the request as plain text

#### Query Parameters

| Name     | Type   | Description                    |
| -------- | ------ | ------------------------------ |
| api\_key | String | The API Key for your Spice app |

#### Headers

| Name                                           | Type   | Description                    |
| ---------------------------------------------- | ------ | ------------------------------ |
| Content-Type<mark style="color:red;">\*</mark> | String | text/plain                     |
| X-API-KEY                                      | String | The API Key for your Spice app |

{% tabs %}
{% tab title="200: OK Query result" %}

{% endtab %}

{% tab title="400: Bad Request The query could not be parsed" %}

{% endtab %}

{% tab title="401: Unauthorized Missing the API Key" %}

{% endtab %}
{% endtabs %}

{% tabs %}
{% tab title="cURL" %}
```bash
curl --request POST \
  --url https://data.spiceai.io/v1/sql \
  --header 'Content-Type: text/plain' \
  --header 'X-API-Key: [api-key]' \
  --data 'select count(number) from eth.recent_blocks'
```
{% endtab %}

{% tab title="Javascript" %}
```javascript
import { SpiceClient } from '@spiceai/spice';

const main = async () => {
  const spiceClient = new SpiceClient('API_KEY');
  const table = await spiceClient.query(
    'select count(number) as num_blocks from eth.recent_blocks'
  );
  console.table(table.toArray());
};
```
{% endtab %}
{% endtabs %}
