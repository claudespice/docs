---
description: Run predictions using pre-trained AI models
---

# Predictions API

{% hint style="info" %}
The Spice AI Predictions API is made available for research purposes only. By accessing and using the APIs, in addition to the [Spice AI Terms](../../legal/terms-of-service.md), you agree to these [terms and conditions](predictions-api-terms-and-conditions.md).
{% endhint %}

Spice AI has trained a number of AI models for predictions on blockchain data. These models come with data included, so all you need to get a glimpse of the future is a simple API request.

## Perform prediction with AI model

<mark style="color:green;">`POST`</mark> `https://data.spiceai.io/v1/predictions`

The model id should be sent in the JSON payload along with an optional Boolean if you want the historical data returned in the response.

#### Query Parameters

| Name                                       | Type   | Description                    |
| ------------------------------------------ | ------ | ------------------------------ |
| api\_key<mark style="color:red;">\*</mark> | String | The API Key for your Spice app |

#### Headers

| Name                                           | Type   | Description                    |
| ---------------------------------------------- | ------ | ------------------------------ |
| Content-Type<mark style="color:red;">\*</mark> | String | application/json               |
| X-API-KEY                                      | String | The API Key for your Spice app |

#### Request Body

| Name                                        | Type   | Description                                                                                     |
| ------------------------------------------- | ------ | ----------------------------------------------------------------------------------------------- |
| model\_id<mark style="color:red;">\*</mark> | String | The id of a pre-trained model                                                                   |
| return\_lookback\_data                      | Bool   | Set to true to include the historical lookback data that the model used to make its prediction. |

{% tabs %}
{% tab title="200: OK Sample query result" %}
```json
{
    "data": {
        "lookback": [{
            "timestamp": 16152356,
            "value": 1.2633415466991442e-9
        }],
        "forecast": [{
            "timestamp": 16152357,
            "value": 1.2633415424900818e-9
        }],
        "now": 16152356
    },
    "duration_ms":2038
}
```
{% endtab %}

{% tab title="400: Bad Request The query could not be parsed" %}
```json
{
    // Response
}
```
{% endtab %}

{% tab title="401: Unauthorized Missing the API Key" %}
```json
{
    // Response
}
```
{% endtab %}
{% endtabs %}

#### Sample request payload

```json
{
    "model_id": "uniswapv3_usdc_eth",
    "return_lookback_data": true
}
```

**Available models**

<table><thead><tr><th width="259">Model Id</th><th>Description</th></tr></thead><tbody><tr><td><code>uniswapv3_usdc_eth</code></td><td>Short horizon forecasting of Uniswap v3 data for the USDC:ETH 0.05% pool <code>0x88e6a0c2ddd26feeb64f039a2c41296fcb3f5640</code></td></tr><tr><td><code>uniswapv3_eth_usdt</code></td><td>Short horizon forecasting of Uniswap v3 data for the ETH:USDT 0.05% pool <code>0x11b815efb8f581194ae79006d24e0d814b7697f6</code></td></tr><tr><td><code>uniswapv3_wbtc_eth</code></td><td>Short horizon forecasting of Uniswap v3 data for the WBTC/ETH 0.05% pool <code>0x4585fe77225b41b697c938b018e2ac67ac5a20c0</code></td></tr><tr><td><code>gasfees_v1</code></td><td>A new Ethereum gas fee prediction model.</td></tr></tbody></table>

#### Requirements and limitations

* An API key is required for all predictions.
