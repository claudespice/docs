# Step 3 - Add AI Model and chat with your app

### Prerequisites

An [OpenAI API Platform](https://platform.openai.com/) account is required is required to obtain an API key and connect to the OpenAI models API.

### Adding a model provider

1. Navigate to **Code** tab.
2. In **Components** sidebar, click **Model Providers** tab, and select **OpenAI**.
3. Enter the **Model name.**
4. Enter the **Model ID**, (e.g. `gpt-4o` ).
5. Set the **OpenAI API Key** secret. The API key will be securely stored and encrypted to ensure your data's safety and privacy.

<figure><img src="../../.gitbook/assets/CleanShot 2024-12-19 at 11.52.06 (1).gif" alt=""><figcaption></figcaption></figure>



6. Add `tools: auto` in the model params to enable the model to access the loaded datasets and enhance its responses. [Learn more](https://docs.spiceai.org/features/large-language-models/runtime_tools) about models and available tools.\
   \
   The final Spicepod configuration should look like this:

```yaml
name: my-first-app
kind: Spicepod
version: v1beta1

datasets:   
  - from: s3://spiceai-demo-datasets/taxi_trips/2024/
    name: samples.taxi_trips
    description: Taxi trips dataset from Spice.ai demo datasets.
    params:
      file_format: parquet
    
models:   
  - from: openai:gpt-4o
    name: gpt-4o
    params:
      endpoint: https://api.openai.com/v1
      openai_api_key: ${secrets:OPENAI_API_KEY}
      tools: auto
    
```

7. Click **Save** in the code toolbar and then **Deploy** in the bottom popup to apply the changes.
8. Navigate to **Playground** and select **AI Chat** in the sidebar, to chat with deployed model.
9. Now you can ask about your data in the chat.

<figure><img src="../../.gitbook/assets/CleanShot 2024-12-19 at 12.22.38@2x.png" alt=""><figcaption><p>Spice AI Chat</p></figcaption></figure>

### Call chat completions API using cURL

Replace `[API-KEY]` in the sample below with your API Key. Learn more about [App API Keys](../../portal/apps/api-keys.md).

{% tabs %}
{% tab title="cURL" %}
```sh
curl --request POST \
      --url 'https://data.spiceai.io/v1/chat/completions' \
      --header 'Content-Type: application/json' \
      --header 'X-API-KEY: 31393037|8f2f6125e7b8487f80964041c123d3c3' \
      --data '{ "messages": [{ "role": "user", "content": "Hello!" }], "model": "gpt-4o" }'
```
{% endtab %}
{% endtabs %}

<figure><img src="../../.gitbook/assets/CleanShot 2024-12-19 at 12.42.00@2x.png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
Raise issues or feedback with our team on [Discord](https://discord.gg/kZnTfneP5u).
{% endhint %}
