---
description: Query web3 data with SQL via the async HTTP API
---

# Async HTTP API

Blockchain and contract data may be queried by posting SQL to the `/v1/sql` API. See [Tables](broken-reference) for a list of tables to query or browse the example queries listed in the menu.

By posting a JSON payload specifying list of query completion `notifications` results can be fetched asynchronously once the query has completed.

Webhooks are currently the only supported type of query completion notification. Get in touch on Discord to suggest other types of notification methods.

Results will only be available for fetching for 20 mins after the query was made.

#### Sample request payload

```json
{
  "sql": "SELECT number, \"timestamp\", hash, transaction_count, gas_used FROM eth.recent_blocks LIMIT 10",
  "notifications": [
    {
      "name": "test notification",
      "type": "webhook",
      "uri": "https://webhook.site/b6f7a959-973e-4bc3-9901-67b397aa55e4"
    }
  ]
}
```

#### Requirements and limitations

* An API key is required for all SQL queries.
* Results are limited to 500 rows. Use `offset` and `limit` to page through results.
* Requests are limited to 90 seconds.

## Perform an async SQL query

<mark style="color:green;">`POST`</mark> `https://data.spiceai.io/v1/sql`

The SQL query should be sent in the JSON payload along with the list of notification configs.

#### Query Parameters

| Name     | Type   | Description                    |
| -------- | ------ | ------------------------------ |
| api\_key | String | The API Key for your Spice app |

#### Headers

| Name                                           | Type   | Description                    |
| ---------------------------------------------- | ------ | ------------------------------ |
| Content-Type<mark style="color:red;">\*</mark> | String | application/json               |
| X-API-KEY                                      | String | The API Key for your Spice app |

#### Request Body

| Name                                            | Type   | Description                    |
| ----------------------------------------------- | ------ | ------------------------------ |
| sql<mark style="color:red;">\*</mark>           | String | The SQL query                  |
| notifications<mark style="color:red;">\*</mark> | Array  | Array of notification objects. |

{% tabs %}
{% tab title="200: OK Sample query result" %}
```json
{
	"sql": "SELECT number, \"timestamp\", hash, transaction_count, gas_used FROM eth.recent_blocks LIMIT 10",
	"notifications": [
		{
			"name": "test notification",
			"type": "webhook",
			"uri": "https://webhook.site/b6f7a959-973e-4bc3-9901-67b397aa55e4"
		}
	]
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

## Fetch SQL query results

<mark style="color:blue;">`GET`</mark> `https://data.spiceai.io/v1/sql/{query_id}`

Use the queryId returned from the query request or from the body of the webhook notification to fetch the results.

#### Path Parameters

| Name                                        | Type   | Description             |
| ------------------------------------------- | ------ | ----------------------- |
| query\_id<mark style="color:red;">\*</mark> | String | The ID of the SQL query |

#### Query Parameters

| Name   | Type   | Description                                                                 |
| ------ | ------ | --------------------------------------------------------------------------- |
| offset | Number | The row offset to fetch results from. Use this to page through results.     |
| limit  | Number | The limit of rows to return in each response. Max limit per request is 500. |

{% tabs %}
{% tab title="200: OK Sample response" %}
```json
{
	"rowCount": 10,
	"schema": [
		{
			"name": "number",
			"type": {
				"name": "BIGINT"
			}
		},
		{
			"name": "timestamp",
			"type": {
				"name": "BIGINT"
			}
		},
		{
			"name": "hash",
			"type": {
				"name": "VARCHAR"
			}
		},
		{
			"name": "transaction_count",
			"type": {
				"name": "BIGINT"
			}
		},
		{
			"name": "gas_used",
			"type": {
				"name": "BIGINT"
			}
		}
	],
	"rows": [
		{
			"number": 16273648,
			"timestamp": 1672114715,
			"hash": "0xa5668e81dfd3cb6155ad07665363df3ae5e270242f58e34870272afc0deec5aa",
			"transaction_count": 66,
			"gas_used": 5414783
		},
		{
			"number": 16273647,
			"timestamp": 1672114703,
			"hash": "0x83fdc68274dc6e7907adb072f88c4f3aa26e1c718ad2064553d2eee569c9e421",
			"transaction_count": 213,
			"gas_used": 21922917
		},
		{
			"number": 16273646,
			"timestamp": 1672114691,
			"hash": "0x8f99b7699ca1949d2be16d5cce033a42808ff9da9a63aa80a10d2b190b6c8781",
			"transaction_count": 159,
			"gas_used": 14368097
		},
		{
			"number": 16273645,
			"timestamp": 1672114679,
			"hash": "0x71347e18506a945ed3346655bb63830e7f9c8eb630fcb5728523f6f77de68c65",
			"transaction_count": 162,
			"gas_used": 14982921
		},
		{
			"number": 16273644,
			"timestamp": 1672114667,
			"hash": "0x7812595b1e4ab024a229cf1ac407588a7c62d318872e5fecff7f5a4c78f6dead",
			"transaction_count": 85,
			"gas_used": 8940413
		},
		{
			"number": 16273643,
			"timestamp": 1672114655,
			"hash": "0xf5f937c0624af97e1a9a4277f35c430bcb6b2cff661064a16f998d07e509862a",
			"transaction_count": 259,
			"gas_used": 29640358
		},
		{
			"number": 16273642,
			"timestamp": 1672114643,
			"hash": "0x680b1b99b241498f13456c75019b09609973f7a50b2419ede31a344ca228916d",
			"transaction_count": 81,
			"gas_used": 7434071
		},
		{
			"number": 16273641,
			"timestamp": 1672114631,
			"hash": "0x5bc66ed23d78856a42313ec712824cfd51861a2aadf1fe5e8c022b82d2124507",
			"transaction_count": 213,
			"gas_used": 21529500
		},
		{
			"number": 16273640,
			"timestamp": 1672114619,
			"hash": "0x82a1483c46e64422c1d402cf462fd35220c56eb2d94b52fcc357b0a04bc3de73",
			"transaction_count": 159,
			"gas_used": 14864730
		},
		{
			"number": 16273639,
			"timestamp": 1672114607,
			"hash": "0x78e877d33719f1c449100fa4d8118cac9e0f94d56cfa45e48fb7fd48469f2e88",
			"transaction_count": 188,
			"gas_used": 15786409
		}
	]
}
```
{% endtab %}
{% endtabs %}
