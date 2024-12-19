# Step 2 - SQL Query

### Execute a SQL query in the Playground

Use the [Playground](broken-reference/) to experiment with SQL queries, AI Chats, APIs, and models. It includes:

* A [SQL Query Editor](../../portal/sql-query-editor.md) for experimentation.
* AI Chat
* Interactive API clients and sample code.

<figure><img src="../../.gitbook/assets/uniswap.gif" alt=""><figcaption><p>SQL Query Editor: querying for UniswapV2 Pool Liquidity Stats</p></figcaption></figure>

### Execute a SQL query using cURL

Once you've set up a [Spice.ai Cloud account](portal-login.md), replace `[API-KEY]` in the sample below with your API Key.

{% tabs %}
{% tab title="cURL" %}
```bash
curl --request POST \
  --url 'https://data.spiceai.io/v1/sql' \
  --header 'Content-Type: text/plain' \
  --header 'X-API-KEY: [API-KEY]'
  --data 'select * from eth.recent_blocks order by "timestamp" desc'
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
Visit the [FAQ](../faq.md) page for more details.
{% endhint %}

### Call the API

To get an API key, set up a [Spice.ai Cloud account](portal-login.md).
