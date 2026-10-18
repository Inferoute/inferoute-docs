# Automatic model selection

You can leave the model choice to Inferoute. Send your price and speed limits, and Inferoute picks the cheapest approved model that can serve the request.

## How to ask

Omit `model`, or set it to `auto`. For example:

```json
{
  "messages": [
    {
      "role": "user",
      "content": "Write a short product summary."
    }
  ],
  "inferoute": {
    "sort": "cost",
    "min_tokens_per_second": 20,
    "max_input_price_per_1m": 0.5,
    "max_output_price_per_1m": 1.0
  }
}
```

The reply includes a `model` field. That is the model that served the request.

`sort` defaults to `cost`, which prefers a lower price. `throughput` prefers faster providers. See [Request routing options](request-routing-options.md) for the other fields.

## What counts as able to serve it

Inferoute reads the body:

| Body | Models considered |
|------|-------------------|
| `messages` or `prompt` | Chat models |
| `input` | Embedding models |
| `tools` or `tool_choice` (other than `none`) | Chat models that support tool calling |

Only approved models are eligible. A model a provider loaded that is not on the approved list is skipped in this mode. Name that model yourself if you want it.

Your max price (profile, key, and the request) and `min_tokens_per_second` still apply. If **Prefer own providers** is on, your own clusters are tried first when one of them can serve the request.

If nothing qualifies, you get **404**.

## Related

- [Using the API](using-the-api.md)
- [Request routing options](request-routing-options.md)
- [Max price limits](max-price-limits.md)
- [Prefer own providers](prefer-own-providers.md)
