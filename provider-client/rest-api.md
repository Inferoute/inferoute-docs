# Local REST API

The Provider Client exposes a small HTTP API on the machine where it runs — for health, busy status, and a status dashboard. These routes are **local only**. Inferoute does not call them through your tunnel; health is sent from the client to Inferoute on a schedule.

The base URL is the client’s server (host and port from config). For example, `http://localhost:8080`. The default bind is **127.0.0.1**, so these routes are only reachable from that machine.

If the client runs in Docker, the tunnel does not need a published port. To open the dashboard from the host, set **server.host** to `0.0.0.0` and publish the port to loopback on the host (for example `-p 127.0.0.1:8080:8080`). See [Configuration](configuration.md).

There are no `/health` or `/busy` aliases — use the `/api/...` paths below.

## GET /

Browser status page with the same information as the terminal UI (session, models, GPU, recent requests). Refreshes automatically.

## GET /api/status

JSON snapshot of that status page. Includes session status, a masked provider API key, models, GPU, recent requests, errors, last health update, and the tunnel URL when one is active.

## GET /api/health

Current health snapshot: GPU (when available), models from the local LLM server, Cloudflare tunnel fields, and `provider_type`.

## GET /api/busy

Whether the client considers itself busy.

```json
{ "busy": false }
```

`busy` is `true` when NVIDIA utilization is above **20%**, or when an inference request is already in flight (including on macOS).

## Inference routes (not for operators)

Inferoute sends work to **POST /v1/chat/completions** and **POST /v1/completions** through the Cloudflare tunnel. Those routes require a signed `X-Request-Id` from the platform. Do not call them yourself — you will get **401**.

When the GPU is busy and the request is not a follow-up for the same session, the client returns **503** `{"error":"GPU is busy"}` so Inferoute can try another provider. Unverified models return **403**. LLM failures return **502**.

## Related

- [How it works](how-it-works.md)
- [Configuration](configuration.md)
