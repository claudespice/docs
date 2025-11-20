---
description: Frequently asked questions
---

# FAQ

### What data sources do you have?

Spice currently supports [**Ethereum**](reference/sql-query-tables/) (including [**Beacon**](reference/sql-query-tables/goerli/beacon-chain-tables/) & [**Goerli**](reference/sql-query-tables/goerli/)), [**Bitcoin**](reference/sql-query-tables/bitcoin/), and [**Polygon**](reference/sql-query-tables/polygon/) blockchain data along with [**ENS**](reference/sql-query-tables/ethereum/token-tables-1/), **DeFi/DEX** ([**Sushiswap**](reference/sql-query-tables/ethereum/sushiswap-tables/)**,** [**Uniswap-V2**](reference/sql-query-tables/ethereum/uniswap-v2-tables/)**,** [**Uniswap-V3**](reference/sql-query-tables/ethereum/uniswap-v2-tables/)**,** [**Aave**](reference/sql-query-tables/ethereum/aave-v2-tables/)), [**NFT**](broken-reference) and [**token**](reference/sql-query-tables/ethereum/token-tables/) specific data.

Digital asset/cryptocurrency [spot/latest](api/prices.md) and full [historical prices](api/prices.md) data is available from leading exchanges including Coinbase, Gemini, and Binance.

We are adding more chains and contracts prioritized by user feedback every month.

For the all-up list, see [Datasets](datasets.md) and follow the [Release notes](release-notes.md) for updates.

### How much does Spice cost?

Spice is currently in beta and it's free to [get an API key](https://spice.ai), although due to demand there is waitlist.

We plan to always have a free tier and offer free APIs, like the [Gas Fees API](api/ethereum/gas-fees.md).

For customers who need higher request or query limits, service guarantees, or priority support we offer high-value paid tiers based on usage.

### What level of support do you offer?

We offer [best-effort support](broken-reference/) in Discord.

If you'd like higher-priority support or are interested in becoming a [Design Partner](https://www.craft.do/s/bgJFtYzSZwuFXD) with dedicated, enterprise-grade support with an SLA, please get in touch.

### What SQL query engine/language do you support?

We currently use an [Apache Calcite](https://calcite.apache.org/) based query engine and support the ANSI SQL standard.

[Spice Firecache](reference/specifications/dataset-and-view-yaml-specification/firecache.md) is built on [DuckDB](https://duckdb.org/) and uses the DuckDB SQL dialect.

### Can you add \<table> / dataset or index a specific on-chain contract?

Most likely, yes! Hit us up on Slack and we can work with you to add new views/tables or index new contracts. The ability to create private custom tables is on our roadmap.

### Do you support WebSockets or other streaming?

WebSocket support is available for [Design Partners](https://www.craft.do/s/bgJFtYzSZwuFXD). Apache Arrow over gRPC streaming is on our roadmap.

### Do you support alerting?

Not yet, but it is on our roadmap. You can also build your own custom alerting using [Spice Functions](portal/apps/spice-functions/).

### Do you support JDBC/ODBC/ADBC?

Not yet, but it is on our roadmap.
