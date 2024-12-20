---
description: How to use the Spice.ai for GitHub Copilot Extension
icon: github
---

# GitHub Copilot

The [Spice AI GitHub Copilot Extension](https://github.com/marketplace/spice-ai-for-github-copilot) makes it easy to access and chat with external data in GitHub Copilot, enhancing AI-assisted research, Q\&A, code, and documentation suggestions for greater accuracy.

<figure><img src="../.gitbook/assets/Screenshot 2024-10-23 at 15.45.09.png" alt="" width="233"><figcaption><p>The @spiceai extension in GitHub Copilot.</p></figcaption></figure>

Access structured and unstructured data from any Spice data source like GitHub, PostgreSQL, MySQL, Snowflake, Databricks, GraphQL, data lakes (S3, Delta Lake, OneLake), HTTP(s), SharePoint, and even FTP.

Some example prompts:

* **`@spiceai`**` ``What documentation is relevant to this file?`
* **`@spiceai`**` ``Write documentation about the user authentication issue`
* **`@spiceai`**` ``Who are the top 5 committers to this repository?`
* **`@spiceai`**` ``What are the latest error logs from my web app?`

## Installing the Spice.ai for GitHub Copilot Extension

To install the extension, visit the [GitHub Marketplace](https://github.com/marketplace/spice-ai-for-github-copilot) and search for **Spice.ai**.

<figure><img src="../.gitbook/assets/image (38).png" alt="" width="563"><figcaption><p>The Spice.ai Extension in the GitHub Marketplace.</p></figcaption></figure>

Scroll down, and click **Install it for free**.

<figure><img src="../.gitbook/assets/image (39).png" alt="" width="563"><figcaption><p>Install the Community Edition of the Spice.ai Extension for free.</p></figcaption></figure>

## Configuring the extension

1. Once installed, open Copilot Chat and type `@spiceai`. Press enter.

<figure><img src="../.gitbook/assets/copilot_chat_again.png" alt="" width="375"><figcaption><p>Starting a conversaton with @spiceai</p></figcaption></figure>

2. A prompt will appear to connect to the Spice.ai Cloud Platform.

<figure><img src="../.gitbook/assets/copilot_connect.png" alt="" width="369"><figcaption><p>Connection prompt</p></figcaption></figure>

3. You will need to authorize the extension. Click **Authorize spiceai**.

<figure><img src="../.gitbook/assets/copilot_authorize.png" alt="" width="563"><figcaption><p>Permissions screen for the Spice AI Extension</p></figcaption></figure>

4. To create an account on the Spice.ai Cloud Platform, click **Authorize Spice AI Platform.**

<figure><img src="../.gitbook/assets/copilot_authorize_platform.png" alt="" width="563"><figcaption><p>Authorizing the Spice.ai Cloud Platform</p></figcaption></figure>

5. Once your account is created, you can configure the extension. Select from a set of ready-to-use datasets to get started. You can configure other datasets after setup.

<figure><img src="../.gitbook/assets/copilot_extension_setup.png" alt="" width="563"><figcaption><p>GitHub Copilot Extension Setup page</p></figcaption></figure>

<figure><img src="../.gitbook/assets/copilot_select_datasets.png" alt="" width="361"><figcaption></figcaption></figure>

6. The extension will take up to 30 seconds to deploy and load the initial dataset.

<figure><img src="../.gitbook/assets/copilot_start_instance.png" alt="" width="563"><figcaption><p>GitHub Copilot Extension Deployment page</p></figcaption></figure>

7. When complete, proceed back to **GitHub Copilot Chat**.

## Chatting with Copilot Chat

### Start a new chat

To chat with the **Spice.ai for GitHub Copilot** extension, prefix the message with `@spiceai`

<figure><img src="../.gitbook/assets/copilot_chat_again (2).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
If `@spiceai` does not appear in the popup <mark style="color:red;">(2)</mark>, ensure that all the [installation](github-copilot.md) steps have been followed.&#x20;
{% endhint %}

### Querying which datasets are available

To list the datasets available to Copilot, try `@spiceai What datasets do I have access to?`

<figure><img src="../.gitbook/assets/copilot_what_datasets_again (1).png" alt="" width="563"><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/copilot_chat_results.png" alt="" width="364"><figcaption></figcaption></figure>

### Querying data using SQL

{% stepper %}
{% step %}
### Navigate to [Spice.ai](https://spice.ai) and click <mark style="color:purple;">Portal</mark>&#x20;

<img src="../.gitbook/assets/copilot_portal.png" alt="" data-size="original">
{% endstep %}

{% step %}
### Click the `copilot` app

<img src="../.gitbook/assets/copilot_portal_app.png" alt="" data-size="original">

This will open the Spice.ai playground
{% endstep %}

{% step %}
### List the tables available

Run `show tables` to list the tables that are available to the Copilot extension

<img src="../.gitbook/assets/copilot_show_tables.png" alt="" data-size="original">
{% endstep %}

{% step %}
### Querying repository content

To query the GitHub content, query one of the tables above like so:

> `SELECT name, path, url, content_embedding FROM react.docs LIMIT 10;`

<img src="../.gitbook/assets/copilot_select.png" alt="" data-size="original">
{% endstep %}
{% endstepper %}

## Reset the Copilot instance

{% stepper %}
{% step %}
### Navigate to [spice.ai](https://spice.ai) and click <mark style="color:purple;">Portal</mark>

<img src="../.gitbook/assets/copilot_portal.png" alt="" data-size="original">
{% endstep %}

{% step %}
### Open the account menu in the top-right corner

<img src="../.gitbook/assets/copilot_reset_user.png" alt="" data-size="original">
{% endstep %}

{% step %}
### Click <mark style="color:purple;">Account Settings</mark>

<img src="../.gitbook/assets/copilot_reset_settings.png" alt="" data-size="original">
{% endstep %}

{% step %}
### Click <mark style="color:purple;">GitHub Copilot</mark>

<img src="../.gitbook/assets/copilot_reset_copilot.png" alt="" data-size="original">
{% endstep %}

{% step %}
### Click <mark style="color:purple;">Reset GitHub Copilot config</mark>

<img src="../.gitbook/assets/copilot_reset_config.png" alt="" data-size="original">
{% endstep %}
{% endstepper %}

