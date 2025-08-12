---
description: Add a dataset and query it using SQL Query in the Playground
icon: circle-3
---

# Add a Dataset and query data

To add a dataset to the Spice app, navigate to the [**Code**](../../portal/app-spicepod/) tab.

Use the **Components sidebar** on the right to select from available **Data Connectors**, **Model Providers**, and ready-to-use **Datasets**.

### Adding a ready-to-use Dataset

1. Navigate to **Code** tab.
2. In **Components** sidebar, click the **Datasets** tab.

<figure><img src="../../.gitbook/assets/CleanShot 2024-12-19 at 11.00.59@2x.png" alt=""><figcaption><p>Spice.ai app Code configuration</p></figcaption></figure>

3. Select and add the **NYC Taxi Trips** dataset
   1. Note the configuration has been added to the editor

<figure><img src="../../.gitbook/assets/CleanShot 2024-12-19 at 11.00.38@2x.png" alt=""><figcaption><p>Add the NYC Taxi Trips dataset</p></figcaption></figure>

4. Click **Save** in the code toolbar and then **Deploy** on popup card that appears in the bottom right.

<figure><img src="../../.gitbook/assets/CleanShot 2024-12-19 at 11.04.22.gif" alt=""><figcaption></figcaption></figure>

5. Navigate to the [**Playground**](../../portal/playground/) tab, open the dataset reference, and click on the `spice.samples.taxi_trips` dataset to insert a sample query into the SQL editor. Then, click **Run Selection**.

<figure><img src="../../.gitbook/assets/CleanShot 2024-12-19 at 11.09.40.gif" alt=""><figcaption><p>Exexuting the sample query for the NYC Taxi Trips dataset.</p></figcaption></figure>

### \[Optional] Execute a SQL query using cURL

6. Go app **Settings** and copy one of the app API Keys.

<figure><img src="../../.gitbook/assets/CleanShot 2024-12-19 at 12.25.51@2x.png" alt=""><figcaption><p>Getting an API Key from the app Settings.</p></figcaption></figure>

7. Replace `[API-KEY]` in the sample below with your API Key and execute from a terminal.

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

<figure><img src="../../.gitbook/assets/CleanShot 2024-12-19 at 12.34.32@2x.png" alt=""><figcaption><p>Showing results from executing a sample NYC Taxi Trips dataaset query using cURL.</p></figcaption></figure>

🎉 Congratulations, you've now added a dataset and queried it.

Continue to [Step 4 to add an AI Model and chat with the dataset](step-3-add-ai-model-and-chat-with-your-app.md).

{% hint style="info" %}
Need help? Ask a question, raise issues, and provide feedback to the Spice AI team on [Discord](https://discord.gg/kZnTfneP5u).
{% endhint %}
