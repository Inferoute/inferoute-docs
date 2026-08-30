# Local REST API

The Provider Client exposes a small HTTP API on the machine where it runs — for health, busy status, and a status dashboard. These routes are **local only**. Inferoute does not call them through your tunnel; health is sent from the client to Inferoute on a schedule.

- **GET /** — Browser status page with the same information as the terminal UI (session, models, GPU, recent requests). Refreshes automatically.
- **GET /api/status** — JSON snapshot of that status page.
- **GET /api/health** — Current health status: GPU information (when available), available LLM models, and related provider state.
- **GET /api/busy** — Whether the GPU is currently busy. Returns a JSON body with a boolean (for example `true` or `false`).

The base URL is the client’s server (host and port from config). For example, `http://localhost:8080`. The default bind is **127.0.0.1**, so the dashboard is only reachable from that machine.

If the client runs in Docker, the tunnel does not need a published port. To open the dashboard from the host, set **server.host** to `0.0.0.0` and publish the port to loopback on the host (for example `-p 127.0.0.1:8080:8080`). See [Configuration](configuration.md).

## Related

- [How it works](how-it-works.md)
- [Configuration](configuration.md)
