# Local REST API

The Provider Client exposes a small local HTTP API for health and busy status.

- **GET /api/health** — Current health status: GPU information (when available), available LLM models, and related provider state.
- **GET /api/busy** — Whether the GPU is currently busy. Returns a JSON body with a boolean (e.g. `true` or `false`).

These endpoints are used by the Inferoute platform and can be used for local monitoring or scripting. The base URL is the client’s server (host and port from config, e.g. `http://localhost:8080`).
