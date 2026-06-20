# FAQ

## General

### What is the Inferoute Provider Client?

A lightweight service that runs on Ollama or vLLM provider machines. It monitors GPU resources, reports health to Inferoute, and handles inference requests by forwarding them to your local Ollama or vLLM instance.

### What platforms are supported?

- **Linux with NVIDIA GPU:** Full GPU monitoring and busy status from utilization. See [Setup: Linux](setup-linux.md).
- **macOS with Apple GPU:** Basic GPU info (model, core count); busy is always reported as false. See [Setup: macOS](setup-mac.md).

### How do I configure the client?

The client reads a YAML config file (server, provider, Cloudflare tunnel, logging). A default config is used if the file is missing. See [Configuration](configuration.md).

### How do I delete a cluster from my account?

Use the dashboard: **Clusters** → select your cluster → **Settings** → **Delete cluster** (Danger zone). That stops inference and revokes API keys; it does not uninstall the client on your machine. See [Deleting and managing clusters](../provider/deleting-clusters.md).

## Model management

### When I add new models to Ollama, does the client pick them up?

Yes. New models are detected on each health check (every 5 minutes) and automatically registered with pricing from the Inferoute API.

### How does model pricing work?

Pricing is **per cluster**, not account-wide. At startup the client discovers local models, applies default prices from Inferoute, and registers each model for **that cluster only**. For example, **inferoute-cluster1** and **inferoute-cluster2** can charge different prices for the same model name.

New models added later are registered during the next health cycle (every 5 minutes).

See [Model pricing](../provider/model-pricing.md) for editing prices in the dashboard.

### Can I change model pricing after registration?

Yes. Open **Clusters** → select a cluster → **Models** tab to edit prices for that cluster only. You can also use the provider API with that cluster’s API key (see the OpenAPI reference in this GitBook space).

Changing prices on one cluster does not change another. See [Model pricing](../provider/model-pricing.md).

### What are GGUF models?

GGUF (GPT-Generated Unified Format) is a model format used by Ollama:

- More memory-efficient than FP16 via quantization.
- Often faster on consumer hardware.
- Various quantization levels (for example Q4_K_M, Q5_K_M) trade size for quality.

When using the Inferoute API, Ollama models use the **gguf/** prefix. For example, `gguf/llama2`.

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
2. If present, the request signature in `X-Request-Id` is validated with Inferoute; invalid requests get **401**.
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

## Related

- [How it works](how-it-works.md)
- [Model pricing](../provider/model-pricing.md)
- [Deleting and managing clusters](../provider/deleting-clusters.md)
