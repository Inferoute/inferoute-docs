# FAQ

## General

### What is the Inferoute Provider Client?

A lightweight Go service that runs on Ollama or vLLM provider machines. It monitors GPU resources, reports health to the central Inferoute system, and handles inference requests by forwarding them to your local Ollama or vLLM instance.

### What platforms are supported?

- **Linux with NVIDIA GPU:** Full GPU monitoring and busy status from utilization.
- **macOS with Apple GPU:** Basic GPU info (model, core count); busy is always reported as false.

### How do I configure the client?

The client reads a YAML config file (server, provider, Cloudflare tunnel, logging). A default config is used if the file is missing. See [Configuration](configuration.md).

## Model management

### When I add new models to Ollama, does the client pick them up?

Yes. New models are detected on each health check (every 5 minutes) and automatically registered with pricing from the Inferoute API.

### How does model pricing work?

At startup the client discovers local models, fetches pricing from the central API, and registers each model (model-specific or default pricing). New models added later are registered during the next health cycle.

### Can I change model pricing after registration?

Yes. Use the provider API (e.g. `/api/provider/models/{model_id}`) or update pricing in your account on the Inferoute platform.

### What are GGUF models?

GGUF (GPT-Generated Unified Format) is a model format used by Ollama:

- More memory-efficient than FP16 via quantization.
- Often faster on consumer hardware.
- Various quantization levels (e.g. Q4_K_M, Q5_K_M) trade size for quality.

When using the Inferoute API, Ollama models use the **gguf/** prefix (e.g. `gguf/llama2`).

## Cloudflare Tunnel

### How does the tunnel URL get set?

The client does not use a hardcoded URL. On startup it requests a tunnel from the Inferoute platform; the platform returns a one-time token and hostname. The client starts cloudflared with that token. You do not need to manually update any URL in config.

### Do I need to run cloudflared myself?

No. The client starts and supervises cloudflared. You only need to ensure the install script has installed the cloudflared binary (or install it yourself if you installed the client manually).

### What if the tunnel process stops?

The client supervises cloudflared and will restart it if it exits, with exponential backoff on repeated failures. Check logs if the tunnel stays down.

## Health and monitoring

### How often does the client report health?

Every **5 minutes**. It also exposes **GET /api/health** (or /health) for on-demand status.

### What is in a health report?

- GPU info (type, driver, CUDA, memory, utilization where available).
- Available models from the local LLM server.
- Current Cloudflare tunnel URL (when configured).

### How is “GPU busy” determined?

- **Linux (NVIDIA):** Utilization above 20% → busy.
- **macOS:** Always reported as not busy.
- No GPU monitoring: not busy.

## Inference requests

### How are inference requests handled?

1. If the GPU is busy → **503 Service Unavailable** (orchestrator can try another provider).
2. If present, HMAC in `X-Request-Id` is validated via the central `/api/provider/validate_hmac` endpoint; invalid → **401**.
3. Valid requests are proxied to the local Ollama or vLLM server.

### What endpoints does the client expose for inference?

OpenAI-compatible:

- **POST /v1/chat/completions**
- **POST /v1/completions**

## Logging

### How do I configure logging?

In `config.yaml`, under **logging:** set level (`debug`, `info`, `warn`, `error`), `log_dir`, `max_size`, `max_backups`, `max_age`. See [Configuration](configuration.md).

### What if GPU monitoring isn’t available?

The client still runs: health reports omit or null out GPU data and the GPU is reported as not busy. You can monitor activity via the console UI and log files.
