# How It Works

The Provider Client is a single Go application built from these components:

- **Configuration** (`pkg/config`) — YAML config, env overrides
- **GPU monitoring** (`pkg/gpu`) — nvidia-smi (Linux) or system_profiler (macOS)
- **Health reporting** (`pkg/health`) — collect metrics, report to orchestrator
- **Ollama / vLLM client** (`pkg/ollama`, etc.) — talk to local LLM server
- **HTTP server** (`pkg/server`) — health, busy, inference endpoints
- **Structured logging** (`pkg/logger`) — Zap, rotation, multiple outputs

## Health monitoring and reporting

1. **Metrics:** GPU type, driver/CUDA, memory, utilization (Linux: nvidia-smi; macOS: system_profiler). Available models from the local Ollama/vLLM API. Cloudflare tunnel URL when configured.
2. **Reporting:** Reports are sent to the central system every **5 minutes**. The client also exposes a local **GET /health** (or /api/health) that returns the same information on demand.
3. **Busy check:** **GET /busy** (or /api/busy) returns whether the GPU is busy. On Linux, utilization above 20% is treated as busy; on macOS the GPU is always reported as not busy.

## Model pricing and registration

- **At startup:** One-time model pricing init: discover models from the local LLM server, fetch prices from the central API (`/api/model-pricing/get-prices`), then register each model via **POST /api/provider/models** (model-specific or default pricing). Conflicts (e.g. model already exists) are handled gracefully.
- **Ongoing:** Every health cycle (5 min), the client checks for new models and auto-registers them with pricing. No restart needed when you add models.

## Inference request handling

1. **GPU busy:** If the GPU is busy, the client responds with **503 Service Unavailable** so the orchestrator can try another provider.
2. **HMAC validation:** If the request includes `X-Request-Id` (HMAC), the client validates it via the central **/api/provider/validate_hmac** endpoint. Invalid HMAC → **401 Unauthorized**.
3. **Proxy:** On success, the request is forwarded to the local Ollama or vLLM server. The client supports **POST /v1/chat/completions** and **POST /v1/completions** (OpenAI-compatible).

## Cloudflare tunnel lifecycle

- On startup the client calls **POST /api/cloudflare/tunnel/request** with the local service URL.
- The central system returns a one-time token and hostname. The client starts **cloudflared** with that token and keeps it running.
- The tunnel URL is used for all communication with the central system. The client supervises cloudflared (restarts if it exits, exponential backoff on failures).
- On shutdown, the client stops the tunnel process. No manual tunnel URL configuration is required—the platform provides the URL.
