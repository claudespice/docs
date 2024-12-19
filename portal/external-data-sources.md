---
description: Connect external data sources to Spice.ai
---

# External Data Sources

Access and query external data sources in addition to community datasets including the ability to execute SQL joins across both by [creating custom datasets](datasets-and-views.md).

External Data Sources initially supports connecting to [PostgreSQL](https://www.postgresql.org/) and [MySQL](https://www.mysql.com/) with more data sources coming soon.

### Adding an External Data Source

External Data Sources are added and managed through [organizations](../portal/organizations.md) and are available to all Spice applications within the organization. They are private and are not visible or accessible to applications in other organizations.

Navigate to the organization's **Settings** and then the **Data Sources** section.

<figure><img src="../.gitbook/assets/Screenshot 2023-10-26 at 12.38.12 AM.png" alt=""><figcaption><p>Adding a new External Data Source to Spice.ai</p></figcaption></figure>

Click **Add Data Source** to show the **New Data Source** dialog.

<figure><img src="../.gitbook/assets/Screenshot 2023-11-29 at 3.22.26 PM.png" alt=""><figcaption><p>List of available External Data Sources.</p></figcaption></figure>

Select the data source and then complete the required connection details.

<figure><img src="../.gitbook/assets/Screenshot 2023-10-26 at 4.55.03 PM.png" alt=""><figcaption><p>Adding a PostgreSQL Data Source.</p></figcaption></figure>

Once the data source is connected the data source will be made available through its **name**, which will be the schema name for SQL queries. E.g. naming the data source connection "**mydb**" will enable selecting tables with the SQL `SELECT * FROM`` `**`mydb`**`.{table}.`, click the vertical dots to the right of the connection to edit or delete it.

To edit or delete the data source, click the vertical ellipses menu.

<figure><img src="../.gitbook/assets/Screenshot 2023-10-26 at 4.59.58 PM.png" alt=""><figcaption><p>Managing an existing data source</p></figcaption></figure>
