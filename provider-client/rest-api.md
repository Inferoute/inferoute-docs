# Local REST API

The Provider Client exposes a small local HTTP API for health and busy status.

- **GET /api/health** — Current health status: GPU information (when available), available LLM models, and related provider state.
- **GET /api/busy** — Whether the GPU is currently busy. Returns a JSON body with a boolean (for example `true` or `false`).

These endpoints are used by Inferoute and can be used for local monitoring or scripting. The base URL is the client’s server (host and port from config). For example, `http://localhost:8080`.

## Related

- [How it works](how-it-works.md)
- [Configuration](configuration.md)
