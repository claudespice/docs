---
description: Best practices and tips for getting the most out of Spice
---

# Best Practices

### Use `recent` tables

If you only need recent/near-real-time data, then use the `recent` tables. For example:

```sql
eth.recent_blocks
eth.recent_transactions
eth.recent_logs
eth.recent_token_transfers
eth.recent_traces
eth.recent_token_mints
btc.recent_blocks
btc.recent_transactions
btc.recent_transaction_inputs
btc.recent_transaction_outputs
```

These tables include the last 30 mins of data and are very fast to query.

### Improve performance with ORDER BY, LIMIT, and OFFSET

You can significantly improve the performance of your SQL query by using limits and offsets on indexed columns. For example to fetch blocks in 1M block chunks:

```
SELECT *
FROM eth.blocks
WHERE number > 12000000
ORDER BY number
LIMIT 1000000
OFFSET 0
```

```
SELECT *
FROM eth.blocks
WHERE number > 12000000
ORDER BY number
LIMIT 1000000
OFFSET 1000000
```

Examples of indexed columns:

* [Ethereum core tables](https://docs.spice.ai/reference/sql-query-tables#improving-query-performance-indexed-columns)
* [NFT tables](https://docs.spice.ai/reference/sql-query-tables/nft-tables#improving-query-performance-indexed-columns)

### Use the Apache Arrow API and SDKs

The [Apache Arrow API](sql-query/apache-arrow-flight-api.md) uses Apache Arrow Flight to deliver results over a high-performance connection. There is no limit to the number of results you can pull through the Arrow API while the HTTP API is [limited to results of 500 rows](../reference/limitations.md).

SDKs like the [Python SDK](../sdks/python-sdk/) always use the Arrow API, so they are a convenient way to access Spice data.

### Combine SQL with Python or other languages

Because the Arrow API makes it convenient to fetch data into pandas and NumPy formats, it's easy to use popular data science libraries and tools. Instead of doing everything in SQL, leverage both SQL and your client programming language to get the job done.
