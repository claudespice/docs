# Grafana

Create custom dashboards and visualisations using the Spice.ai data source plugin for Grafana. This plugin can be found in [Grafana plugins catalog](https://grafana.com/grafana/plugins/spiceai-spicexyz-datasource/), and is also available on [GitHub](https://github.com/spiceai/grafana-spice.xyz-datasource/).

## Installation

For more information, visit the docs on [plugin installation](https://grafana.com/docs/grafana/latest/plugins/installation/).

### Installing using Grafana CLI

```
grafana-cli plugins install
```

### Installing with Docker

Set `GF_INSTALL_PLUGINS` env variable, to include the plugin.

```
GF_INSTALL_PLUGINS="spiceai-spicexyz-datasource"
```

## Configuration

After installing a plugin, go to connections -> your connections -> data sources, and search for Spice.ai.

Set API Key from your Spice.ai application and click **Save & test**:

<figure><img src="../../.gitbook/assets/CleanShot 2023-07-18 at 16.04.58@2x.png" alt=""><figcaption></figcaption></figure>

## Creating visualisations

Create new Grafana panel and select `Spice.ai` data source, edit the query and configure visualisation.

<figure><img src="../../.gitbook/assets/CleanShot 2023-07-18 at 16.08.00@2x.png" alt=""><figcaption></figcaption></figure>

Visit [Grafana Docs](https://grafana.com/docs/grafana/latest/panels-visualizations/) for more information about panels visualisations, and also check spice queries examples for reference:

[broken-reference](../broken-reference/ "mention")

[example-ethereum-beacon-sql-queries](../../reference/samples-and-examples/example-ethereum-beacon-sql-queries/ "mention")

[Broken link](broken-reference "mention")

[example-bitcoin-queries](../../reference/samples-and-examples/example-bitcoin-queries/ "mention")

[broken-reference](../broken-reference/ "mention")

[Broken link](broken-reference "mention")
