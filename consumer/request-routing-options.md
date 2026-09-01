# Request routing options

You can add optional Inferoute routing preferences to an API request without changing the request sent to providers. Put these fields under `inferoute`; Inferoute uses them to choose a provider, then removes them before forwarding the request.

For example:

```json
{
  "model": "Qwen/Qwen2.5-7B-Instruct",
  "messages": [
    {
      "role": "user",
      "content": "Write a short product summary."
    }
  ],
  "inferoute": {
    "sort": "throughput",
    "min_tokens_per_second": 50,
    "max_input_price_per_1m": 0.5,
    "max_output_price_per_1m": 1.0,
    "session_id": "agent-run-42"
  }
}
```

## Options

| Field | Meaning |
|-------|---------|
| `sort` | Choose `cost` for lower price first (the default), or `throughput` for faster providers first. |
| `min_tokens_per_second` | Only use providers whose recent average output speed is at least this value. Providers with no history yet are still eligible. |
| `max_input_price_per_1m` | Override the API key's max input price for this request. |
| `max_output_price_per_1m` | Override the API key's max output price for this request. |
| `session_id` | Pin follow-up turns in the same conversation to the same provider when it is still healthy. If you omit it, Inferoute still tries to stick to one provider from the conversation content. |

Request price overrides can be lower than your API key limits, or higher up to your **profile default**. They cannot exceed your profile default.

## Your Own Providers

If **Prefer own providers** is enabled, Inferoute tries your own clusters first. Your own clusters still need to meet `min_tokens_per_second`, but max price (profile, key, and request) only limits marketplace providers.

## Streaming

`"stream": true` is accepted, but Inferoute does **not** stream tokens today. You get the full reply when generation finishes. Routing options do not change that.

## Related

- [Using the API](using-the-api.md)
- [Prefer own providers](prefer-own-providers.md)
- [Max price limits](max-price-limits.md)
