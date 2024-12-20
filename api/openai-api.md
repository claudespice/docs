---
icon: brackets-curly
---

# OpenAI API

## Chat Completions

Spice provides an OpenAI compatible chat completion AI at [https://data.spiceai.io/v1/chat/completions](https://data.spiceai.io/v1/chat/completions). Authorize with the endpoint using an [App API key](../portal/apps/app-api-keys.md).

The App requires a configured and deployed model to respond to chat completion requests.

For more information about using chat completions, refer to the [OpenAI documentation](https://platform.openai.com/docs/api-reference/chat).

### Chat Completion Examples

{% tabs %}
{% tab title="curl" %}
{% code overflow="wrap" lineNumbers="true" %}
```bash
curl https://data.spiceai.io/v1/chat/completions -H "Content-Type: application/json" -d '{"model": "my_model","messages": [{"role": "system","content": "I am just like any other OpenAI server!"},{"role": "user","content": "Thats cool!"}]}' -H "Authorization: Bearer {APP_TOKEN}"
```
{% endcode %}

Example completion response:

{% code overflow="wrap" lineNumbers="true" %}
```json
{
  "id": "chatcmpl",
  "choices": [
    {
      "index": 0,
      "message": {
        "content": "Thank you! Is there anything specific you'd like to know or talk about?",
        "refusal": null,
        "tool_calls": null,
        "role": "assistant",
        "function_call": null
      },
      "finish_reason": "stop",
      "logprobs": null
    }
  ],
  "created": 1734572480,
  "model": "my_model",
  "service_tier": null,
  "system_fingerprint": null,
  "object": "chat.completion",
  "usage": {
    "prompt_tokens": 25,
    "completion_tokens": 16,
    "total_tokens": 41,
    "prompt_tokens_details": {
      "audio_tokens": 0,
      "cached_tokens": 0
    },
    "completion_tokens_details": {
      "accepted_prediction_tokens": 0,
      "audio_tokens": 0,
      "reasoning_tokens": 0,
      "rejected_prediction_tokens": 0
    }
  }
}
```
{% endcode %}
{% endtab %}

{% tab title="Python" %}
This example requires the `openai` Python package.

```
pip install openai
```

Create and run the example Python script to run a completion:

{% code overflow="wrap" lineNumbers="true" %}
```python
from openai import OpenAI

client = OpenAI(api_key="31393036|b0dd04f6892e4a38b3e829b0cea973b1", base_url="https://data.spiceai.io/v1")

response = client.chat.completions.create(
    model="my_model",
    messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "What is the capital of France?"}
    ],
)

print(response.choices[0].message.content)
```
{% endcode %}

Running this example outputs a model response:

{% code lineNumbers="true" %}
```bash
╰─± python test.py
The capital of France is Paris.
```
{% endcode %}
{% endtab %}
{% endtabs %}

