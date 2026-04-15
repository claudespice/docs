---
icon: plug
---

# Grafana

Create custom dashboards and visualizations using the FlightSQL or Infinity Grafana Plugins:

* [FlightSQL Grafana Plugin](https://grafana.com/grafana/plugins/influxdata-flightsql-datasource/)
* [Infinity Grafana Plugin](https://grafana.com/grafana/plugins/yesoreyeram-infinity-datasource/)

## Setup with FlightSQL Grafana Plugin

### Installing FlightSQL Grafana Plugin

Use the [FlightSQL Grafana Plugin installation instructions](https://grafana.com/grafana/plugins/influxdata-flightsql-datasource/?tab=installation) for an installation reference.

In the Grafana installation, navigate to `Administration` -> `Plugins and data` -> `Plugins`. Select "State: All" and search for "FlightSQL" in the Search box. Select "FlightSQL" in the list of plugins.

<figure><img src="../.gitbook/assets/grafana-datasource-2.png" alt="" width="563"><figcaption><p>Navigating to plugins and data in Grafana, and searching for FlightSQL</p></figcaption></figure>

Click the "Install" button to install the plugin. Once installed, a new data source can be added.

### Adding Spice as a data source

Click the "Add new data source" button from the FlightSQL plugin menu.

<figure><img src="../.gitbook/assets/grafana-datasource-3.png" alt="" width="563"><figcaption><p>The FlightSQL plugin menu, once the plugin is installed.</p></figcaption></figure>

In the data source menu, provide the following options:

* `Host:Port` - set to `flight.spiceai.io:443` to connect to the Spice.ai Cloud Platform
* `Auth Type` - set to `token`
* `Token` - set to your [Spice.ai API key](../portal/apps/api-keys.md).&#x20;

Once these options are set, click "Save & Test". Grafana should report "OK" if the configuration is correct.

<figure><img src="../.gitbook/assets/image (42).png" alt="" width="563"><figcaption><p>Setting up a FlightSQL datasource to connect to Spice.ai Cloud Platform</p></figcaption></figure>

### Creating visualizations

Create a visualization using the FlightSQL Spice.AI data source. Select the configured datasource from the list of datasources in the visualization creator.

<figure><img src="../.gitbook/assets/image (43).png" alt="" width="563"><figcaption><p>Select the FlightSQL datasource for the visualization</p></figcaption></figure>

Create your query using the SQL builder, or switch to a plain SQL editor by clicking the "Edit SQL" button. In this example, the query retrieves the latest query execution times from the configured App instance and displays them as a line graph.

<figure><img src="../.gitbook/assets/image (44).png" alt="" width="563"><figcaption><p>A Grafana line graph displaying the most recent query execution times</p></figcaption></figure>

Grafana may not automatically update the visualization when changes are made to the query. To force the visualization to update with new query changes, click the "Query Inspector" button, and click "Refresh".

<figure><img src="../.gitbook/assets/image (45).png" alt="" width="563"><figcaption><p>Manually refreshing the visualization query in FlightSQL</p></figcaption></figure>
