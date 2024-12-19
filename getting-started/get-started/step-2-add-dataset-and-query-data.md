---
description: Connect first dataset
---

# Step 2 - Add Dataset and query data

To configure your Spice app with a custom dataset, navigate to the **Code** tab. On the left, you can directly edit the current App Spicepod configuration. Use the right sidebar to select from available data connectors, model providers, and ready-to-use datasets.

### Adding a sample dataset

1. Navigate to **Code** tab.
2. In **Components** sidebar, click **Datasets** tab.

<figure><img src="../../.gitbook/assets/CleanShot 2024-12-19 at 11.00.59@2x.png" alt=""><figcaption><p>Spice App Code configuration</p></figcaption></figure>

3. Select **NYC Taxi Trips** dataset - that will add a new dataset in entry in configuration code.

<figure><img src="../../.gitbook/assets/CleanShot 2024-12-19 at 11.00.38@2x.png" alt=""><figcaption><p>Add NYC Taxi Trips dataset</p></figcaption></figure>

4. Click **Save** in the code toolbar and then **Deploy** in the bottom popup.

<figure><img src="../../.gitbook/assets/CleanShot 2024-12-19 at 11.04.22.gif" alt=""><figcaption></figcaption></figure>

5. Navigate to the **Playground** tab, open the dataset reference, and click on the `spice.samples.taxi_trips` dataset to insert a sample query into the SQL editor. Then, click **Run Selection**.

<figure><img src="../../.gitbook/assets/CleanShot 2024-12-19 at 11.09.40.gif" alt=""><figcaption></figcaption></figure>

### Execute a SQL query using cURL

Go app **Settings** and copy one of the API Keys. Learn more about [App API Keys](../../portal/apps/app-api-keys.md).

<figure><img src="../../.gitbook/assets/CleanShot 2024-12-19 at 12.25.51@2x.png" alt=""><figcaption></figcaption></figure>

Replace `[API-KEY]` in the sample below with your API Key.

{% tabs %}
{% tab title="cURL" %}
```sh
curl --request POST \
  --url 'https://data.spiceai.io/v1/sql' \
  --header 'Content-Type: text/plain' \
  --header 'X-API-KEY: [API-KEY]' \
  --data 'select * from spice.samples.taxi_trips limit 3'
```
{% endtab %}
{% endtabs %}

<figure><img src="../../.gitbook/assets/CleanShot 2024-12-19 at 12.34.32@2x.png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
Raise issues or feedback with our team on [Discord](https://discord.gg/kZnTfneP5u).
{% endhint %}
