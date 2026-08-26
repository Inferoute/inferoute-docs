# Local REST API

The Provider Client exposes a small local HTTP API for health, busy status, and a status dashboard.

- **GET /** — Browser status page with the same information as the terminal UI (session, models, GPU, recent requests). Refreshes automatically.
- **GET /api/status** — JSON snapshot of that status page.
- **GET /api/health** — Current health status: GPU information (when available), available LLM models, and related provider state.
- **GET /api/busy** — Whether the GPU is currently busy. Returns a JSON body with a boolean (for example `true` or `false`).

The health and busy endpoints are used by Inferoute. You can also use them, and the status dashboard, for local monitoring. The base URL is the client’s server (host and port from config). For example, `http://localhost:8080`.

## Related

- [How it works](how-it-works.md)
- [Configuration](configuration.md)
