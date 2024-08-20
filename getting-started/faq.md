---
description: Frequently asked questions
---

# FAQ

### What's the difference between the Spice.ai Cloud Platform and Spice.ai OSS?

[**Spice.ai OSS**](https://github.com/spiceai/spiceai) is an open-source project created by Spice AI that provides a unified SQL query interface to locally materialize, accelerate, and query data tables sourced from any database, data warehouse, or data lake.

<figure><img src="../.gitbook/assets/OGP.png" alt=""><figcaption><p>Spice.ai OSS</p></figcaption></figure>

**The Spice.ai Cloud Platform** is a data and AI application platform that provides a set of building-block to create data and AI driven applications. Building blocks include a cloud-data-warehouse, ML model training and inference, and a cloud-scale, managed Spice.ai OSS cloud-hosted service.

<figure><img src="../.gitbook/assets/OGP (1).png" alt=""><figcaption><p>The Spice.ai Cloud Platform</p></figcaption></figure>

### What data is available?

Spice.ai is preloaded with community data, including select blockchain datasets from [**Ethereum**](../reference/sql-query-tables/) (including [**Beacon**](broken-reference)) and [**Bitcoin**](../reference/sql-query-tables/bitcoin/) blockchain data along with [**ENS**](../reference/sql-query-tables/ethereum/token-tables-1/), **DeFi/DEX** ([**Sushiswap**](../reference/sql-query-tables/ethereum/sushiswap-tables/)**,** [**Uniswap-V2**](../reference/sql-query-tables/ethereum/uniswap-v2-tables/)**,** [**Uniswap-V3**](../reference/sql-query-tables/ethereum/uniswap-v2-tables/)**,** [**Aave**](../reference/sql-query-tables/ethereum/aave-v2-tables/)), [**NFT**](broken-reference/) and [**token**](../reference/sql-query-tables/ethereum/token-tables/) specific data. Spice.ai also features a comprehensive set of [EigenLayer datasets](../reference/sql-query-tables/ethereum/eigenlayer-tables/).

Datasets can be published to the Spice.ai platform by the community for public or private use using [Datasets and Views](../building-blocks/datasets-and-views.md).

For the all-up list, see [Communtiy Data](../building-blocks/datasets.md) and follow the [Release notes](../reference/release-notes.md) for updates.

### How much does Spice.ai cost?

It's free to [get an API key](https://spice.ai) to use the [Community Edition](pricing/community-edition.md).

For customers who need higher request or query limits, service-level guarantees, or priority support we offer [high-value paid tiers](pricing/) based on usage.

### What level of support do you offer?

We offer enterprise-grade support with an SLA [for Enterprise Plans](pricing/).

For standard plans we offer [best-effort community support](https://github.com/spicehq/cloud-docs/blob/trunk/broken-reference/README.md) in Discord.

### What SQL query engine/language do you support?

Spice.ai Cloud uses an [Apache Calcite](https://calcite.apache.org/) based query engine and supports the ANSI SQL standard.

[Spice Firecache](../reference/specifications/dataset-and-view-yaml-specification/firecache.md) is uses a PostgreSQL dialect and we are working to migrate Spice.ai Cloud to a PostgreSQL compatible dialect.

### Can you add \<table> / dataset or index a specific on-chain contract?

We support [custom views and datasets](../building-blocks/datasets-and-views.md) for adding and publishing new datasets.

### Do you support WebSockets or other streaming?

We do not support WebSockets. Data can be streamed using Apache Arrow Flight over gRPC which is the default when using our [SDKs.](broken-reference)

### Do you support JDBC//ODBC/ADBC?

Not yet, but it is on our roadmap. JDBC/ODB/ADBC is already supported by Spice.ai OSS and is coming to Spice Firecache soon.
