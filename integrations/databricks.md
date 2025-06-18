---
icon: plug
---

# Databricks

{% hint style="info" %}
Databricks OAuth connections require Spice.ai v1.4.0 or higher.
{% endhint %}

### Create Databricks App Connection

To connect Spice Cloud to your Databricks workspace, create a new App connection in your workspace settings with the following configuration:

* **Application Name**: `Spice Cloud Platform`
* **Redirect URLs**: `https://spice.ai/api/integrations/databricks/callback`&#x20;
* **Access scopes**: `All Apis`
* **Client secret generation:** `disabled`

<figure><img src="../.gitbook/assets/databricks-add-connection.png" alt=""><figcaption><p>Add new Databricks OAuth connection</p></figcaption></figure>

After creating the connection, copy the generated OAuth client ID:

<figure><img src="../.gitbook/assets/databricks-connection-client-id.png" alt=""><figcaption><p>Copy Databricks Client ID</p></figcaption></figure>

### Configure Databricks Connection in Spice Cloud

Navigate to the code editor and select the Databricks connector. Choose either SQL Warehouse or Delta Lake mode, then select the OAuth authorization option. Click **Connect Databricks Workspace** to proceed.

<figure><img src="../.gitbook/assets/spicepod-editor (1).png" alt=""><figcaption><p>Databricks connector in code editor</p></figcaption></figure>

Enter the workspace URL and OAuth Client ID generated from the Databricks OAuth app, then click **Connect**.

<figure><img src="../.gitbook/assets/new-databricks-connection (1).png" alt=""><figcaption><p>Set Databricks workspace URL and Client ID</p></figcaption></figure>

After successful authentication in Databricks, you will be redirected back to the Spice app:

{% hint style="info" %}
Note: Databricks authentication credentials are only stored client-side in your browser and never in Spice Cloud.
{% endhint %}

<figure><img src="../.gitbook/assets/connection-success.png" alt=""><figcaption><p>Successful connection</p></figcaption></figure>

Once connected, Databricks datasets, catalogs, and models can use the connection:

<figure><img src="../.gitbook/assets/spicepod-editor-dataset (1).png" alt=""><figcaption><p>Code editor with Databricks catalog, dataset and model</p></figcaption></figure>
