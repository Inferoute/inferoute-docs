# Local REST API

The Provider Client exposes a small HTTP API on the machine where it runs — for health, busy status, and a status dashboard. These routes are **local only**. Inferoute does not call them over the provider connection; health is sent from the client to Inferoute on a schedule.

The base URL is the client’s server (host and port from config). For example, `http://localhost:8080`. The default bind is **127.0.0.1**, so these routes are only reachable from that machine.

If the client runs in Docker, the connection to Inferoute does not need a published port. To open the dashboard from the host, set **server.host** to `0.0.0.0` and publish the port to loopback on the host (for example `-p 127.0.0.1:8080:8080`). See [Configuration](configuration.md).

There are no `/health` or `/busy` aliases — use the `/api/...` paths below.

## GET /

Browser status page with the same information as the terminal UI (session, models, GPU, recent requests). Refreshes automatically.

## GET /api/status

JSON snapshot of that status page. Includes session status, a masked provider API key, models, GPU, recent inference requests, last health update, and whether the client is connected to Inferoute.

## GET /api/health

Current health snapshot: GPU (when available), models from the local LLM server, and `provider_type`.

## GET /api/busy

Whether the client considers itself busy.

```json
{ "busy": false }
```

`busy` is `true` only when every inference slot is full. The default is **1** slot (`max_concurrent_inference`). The same rule applies on Linux, Windows, and macOS. The health report’s GPU `is_busy` flag is a separate check — see [How it works](how-it-works.md).

## Inference routes (not for operators)

Inferoute sends work to **POST /v1/chat/completions**, **POST /v1/completions**, and **POST /v1/embeddings** over the provider connection. Those routes require a signed `X-Request-Id` from the platform. Do not call them yourself — you will get **401**.

When every inference slot is full and the request is not a follow-up for the same session, the client returns **503** `{"error":"GPU is busy"}` so Inferoute can try another provider. Unverified models return **403**. LLM failures return **502**.

## Platform: client version

These are Inferoute platform routes, not the local client API. The client calls them with `X-Inferoute-Client-Version`.

### GET /api/client/version

No API key. Returns the newest release and the oldest release Inferoute still accepts.

```json
{
  "latest": "1.4.0",
  "min_supported": "1.2.0",
  "download_url": "https://github.com/inferoute/inferoute-client/releases/latest",
  "support_window_days": 90
}
```

`min_supported` is an empty string while every release is accepted.

### 426 Upgrade Required

A provider route returns this when the client is older than `min_supported`.

```json
{
  "error": "client_outdated",
  "min_version": "1.2.0",
  "latest_version": "1.4.0",
  "download_url": "https://github.com/inferoute/inferoute-client/releases/latest"
}
```

Successful responses include `X-Inferoute-Latest-Client-Version`. See [Updating the client](updating.md).

## Related

- [How it works](how-it-works.md)
- [Configuration](configuration.md)
