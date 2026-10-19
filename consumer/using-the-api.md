# Using the API

Inferoute exposes an **OpenAI-compatible API** at `https://core.inferoute.com`. Use a **consumer API key** from **Usage** — not a provider cluster key.

You need at least **$1.00** available in your wallet. Requests below that return **402**.

## Create a consumer API key

1. Switch the dashboard to **Consumer** in the sidebar.
2. Open **Usage**.
3. Click **Create API key**.

The full key is shown **once**. Store it somewhere safe.

You can also set a custom [max price](max-price-limits.md) on the key when you create it, and a [monthly spending cap](monthly-spending-caps.md) later on **Settings**.

## First request

```bash
curl https://core.inferoute.com/v1/chat/completions \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "Qwen/Qwen2.5-7B-Instruct",
    "messages": [
      {
        "role": "user",
        "content": "Write a short product summary."
      }
    ]
  }'
```

With the OpenAI Python SDK:

```python
from openai import OpenAI

client = OpenAI(
    base_url="https://core.inferoute.com/v1",
    api_key="YOUR_API_KEY",
)

completion = client.chat.completions.create(
    model="Qwen/Qwen2.5-7B-Instruct",
    messages=[{"role": "user", "content": "Write a short product summary."}],
)
print(completion.choices[0].message.content)
```

Also supported:

- `POST /v1/completions` — send `prompt` instead of `messages`.
- `POST /v1/embeddings` — send `input` as a string or an array of strings.
- `GET /v1/models` — a short list of model ids. It is the busiest models, not every model a provider is serving.

Send one of `messages`, `prompt`, or `input`. Do not send more than one.

Fields you send, other than `inferoute`, are forwarded to the engine. That includes `tools`, `tool_choice`, `response_format`, `temperature`, `seed`, and the rest of the OpenAI body. If you omit `max_tokens`, Inferoute sets it to **16384**.

`tools` only works on a model that supports tool calling. If you name a model that does not, the request returns **400**.

You can omit `model`, or send `"model": "auto"`, and Inferoute picks a model for you. See [Automatic model selection](automatic-model-selection.md).

## Routing options

Add an `inferoute` object to choose providers without changing the payload the model sees. Inferoute strips it before forwarding. See [Request routing options](request-routing-options.md).

## Streaming

`"stream": true` returns Server-Sent Events (`text/event-stream`). Tokens arrive as `data:` lines and the stream ends with `data: [DONE]`.

## Errors

| HTTP | When |
|------|------|
| **400** | Bad body, a request price above your profile default, or `tools` on a model that does not support them |
| **401** | Missing or invalid API key |
| **402** `Insufficient funds` | Wallet available balance is below **$1.00** |
| **402** `spending_cap_exceeded` | That key hit its [monthly spending cap](monthly-spending-caps.md) |
| **404** | No provider can serve that model under your price or throughput limits |

## Related

- [Max price limits](max-price-limits.md)
- [Monthly spending caps](monthly-spending-caps.md)
- [Prefer own providers](prefer-own-providers.md)
- [Request routing options](request-routing-options.md)
- [Automatic model selection](automatic-model-selection.md)
