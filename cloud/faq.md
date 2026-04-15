---
description: Frequently asked questions
---

# FAQ

### What data sources do you have?

Spice supports a wide variety of data connectors for enterprise data sources. See the [Data Connectors](building-blocks/data-connectors/README.md) documentation and follow the [Release notes](release-notes.md) for updates.

### How much does Spice cost?

Spice is currently in beta and it's free to [get an API key](https://spice.ai), although due to demand there is waitlist.

For customers who need higher request or query limits, service guarantees, or priority support we offer high-value paid tiers based on usage.

### What level of support do you offer?

We offer [best-effort support](broken-reference/) in Discord.

If you'd like higher-priority support or are interested in becoming a [Design Partner](https://www.craft.do/s/bgJFtYzSZwuFXD) with dedicated, enterprise-grade support with an SLA, please get in touch.

### What SQL query engine/language do you support?

We currently use an [Apache Calcite](https://calcite.apache.org/) based query engine and support the ANSI SQL standard.

[Spice Firecache](reference/specifications/dataset-and-view-yaml-specification/firecache.md) is built on [DuckDB](https://duckdb.org/) and uses the DuckDB SQL dialect.

### Can you add a custom dataset or table?

Most likely, yes! Hit us up on Slack and we can work with you to add new views/tables. The ability to create private custom tables is on our roadmap.

### Do you support WebSockets or other streaming?

WebSocket support is available for [Design Partners](https://www.craft.do/s/bgJFtYzSZwuFXD). Apache Arrow over gRPC streaming is on our roadmap.

### Do you support alerting?

Not yet, but it is on our roadmap. You can also build your own custom alerting using [Spice Functions](portal/apps/spice-functions/).

### Do you support JDBC/ODBC/ADBC?

Not yet, but it is on our roadmap.
