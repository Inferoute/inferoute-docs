# How It Works

This page explains what the Provider Client does on your machine and how it talks to Inferoute. For install steps, see [Installation](installation.md).

## Overview

The client runs alongside Ollama or vLLM on your machine. It:

1. Reports health (GPU, models, tunnel status) to Inferoute on a schedule.
2. Registers models and initial pricing for **your cluster**.
3. Accepts inference requests from Inferoute and forwards them to your local LLM server.
4. Creates and maintains a **Cloudflare Tunnel** so Inferoute can reach you without open firewall ports.

## Health monitoring

Every **5 minutes**, the client sends a health report to Inferoute. The report includes:

- GPU type, memory, and utilization (on Linux with NVIDIA).
- Which models your local server exposes.
- Your current tunnel URL, when active.

You can also check status locally anytime:

- **GET /api/health** (or **GET /health**) — full health snapshot.
- **GET /api/busy** (or **GET /busy**) — whether the GPU is considered busy.

On Linux with NVIDIA, utilization above **20%** is treated as busy. On macOS, busy is always reported as false.

## Model pricing and registration

**At startup**, the client discovers models on your local LLM server, looks up default prices from Inferoute, and registers each model for **that cluster**.

**Ongoing**, each health cycle checks for new models (for example after you pull a new model into Ollama). New models are registered automatically — you do not need to restart the client.

Initial prices come from platform averages. You set your own prices **per cluster** in the dashboard — see [Model pricing](../provider/model-pricing.md).

Approved marketplace models must match a platform [approved model build](approved-models.md). The client verifies Ollama digests and vLLM weight fingerprints against that allowlist before registration, health reporting, and inference.

**Ollama** — no extra config; digests come from `/api/tags`.

**vLLM** — the client reads the served model id from vLLM and finds weights in the HuggingFace hub cache using the approved `hf_revision`. Optional **`hf_hub_cache`** or **`model_path`** only if your layout is non-standard.

## Inference requests

When Inferoute sends a request to your cluster:

1. **Busy GPU** — If the GPU is busy, the client responds with **503 Service Unavailable** so Inferoute can try another provider.
2. **Authentication** — Valid requests include a signed header; invalid requests get **401 Unauthorized**.
3. **Proxy** — Valid requests are forwarded to your local Ollama or vLLM server. The client supports OpenAI-compatible **POST /v1/chat/completions** and **POST /v1/completions**.

## Cloudflare tunnel

On startup:

1. The client asks Inferoute for a tunnel.
2. Inferoute returns a one-time token and hostname.
3. The client starts **cloudflared** with that token and keeps it running (restarts if it exits).

You do not configure a tunnel URL yourself. On shutdown, the client stops the tunnel process.

## Related

- [Configuration](configuration.md)
- [FAQ](faq.md)
- [Model pricing](../provider/model-pricing.md)
