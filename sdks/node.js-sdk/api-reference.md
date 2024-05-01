# API Reference

## SpiceClient(params)

The top-level object that connects to Spice.ai

* `params.api_key` (string, optional): API key to authenticate with the endpoint
* `params.http_url` (string, optional):&#x20;
* `params.flight_url` (string, optional): URL of the endpoint to use (default: `localhost:50051`, using local spice runtime)

Default connection to local spice runtime:

```javascript
import { SpiceClient } from "@spiceai/spice";

const spiceClient = new Spiceclient();
```

Connect to spice.ai api:

```javascript
import { SpiceClient } from "@spiceai/spice";

const spiceClient = new Spiceclient({
    api_key: 'API_KEY',
    http_url: 'https://data.spiceai.io',
    flight_url: 'flight.spiceai.io:443'
});
```

Or using shorthand:

```javascript
import { SpiceClient } from "@spiceai/spice";

const spiceClient = new SpiceClient('API_KEY');
```

### SpiceClient Methods

**query**(queryText: string, onData: (partialData: [Table](https://arrow.apache.org/docs/js/classes/Arrow\_dom.Table.html)) => void) => [Table](https://arrow.apache.org/docs/js/classes/Arrow\_dom.Table.html)

* `queryText`: (string, required): The SQL query to execute
* `onData`: (callback, optional): The callback function that is used for handling [streaming](streaming.md) data.

`query` returns an Apache Arrow [Table](https://arrow.apache.org/docs/js/classes/Arrow\_dom.Table.html).

To get the data in JSON format, iterate over each row by calling [`toArray()`](https://arrow.apache.org/docs/js/classes/Arrow\_dom.Table.html#toArray) on the table and call [`toJSON()`](https://arrow.apache.org/docs/js/classes/Arrow\_dom.StructRow.html#toJSON) on each row.

```javascript
const table = await spiceClient.query("SELECT * from eth.recent_blocks LIMIT 10")
table.toArray().forEach((row) => {
  console.log(row.toJSON());
});
```

Get all of the elements for a column by calling [`getChild(name: string)`](https://arrow.apache.org/docs/js/classes/Arrow\_dom.Table.html#getChild) and then calling `toJSON()` on the result.

```javascript
const table = await client.query(
  'SELECT number, base_fee_per_gas / 1e9 AS base_fee_per_gas_gwei FROM eth.recent_blocks limit 3'
);

let baseFeeGwei = tableResult.getChild("base_fee_per_gas_gwei");
console.log(baseFeeGwei?.toJSON())
```
