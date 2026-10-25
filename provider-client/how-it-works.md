# How It Works

This page explains what the Provider Client does on your machine and how it talks to Inferoute. For install steps, see [Installation](../getting-started/installation.md). For engine and model selection, see the [setup wizard](setup.md).

## Overview

The client runs alongside the local LLM engine the wizard installed (vLLM, vLLM Metal, or llama.cpp). It:

1. Reports health (GPU and models) to Inferoute on a schedule.
2. Registers models and initial pricing for **your cluster**.
3. Accepts inference requests from Inferoute and forwards them to your local LLM server.
4. Opens an outbound connection so Inferoute can reach you without open firewall ports. It reconnects if the connection drops.

On a multi-node cluster, setup also connects this machine as the master (provider API key) or as a worker (a `join-` code). See [Multi-node clusters](../provider/multi-node-clusters.md).

If **auto_start** is on (the wizard turns this on when it finds the engine binary), the client starts that engine when `llm_url` is down. It uses the serve flags already in `config.yaml` (including whether tool calling is on). It will not start a second copy if the port is already in use, and it will not pull fresh catalog flags at runtime. Re-run [setup](setup.md) after you change model or catalog flags.

## Health monitoring

Every **3 minutes**, the client sends a health report to Inferoute. It also sends one immediately when it becomes busy or free (with a short cooldown). The report includes:

- GPU type, memory, and utilization (on Linux with NVIDIA).
- Which models your local server exposes.

Inferoute detects **cluster country** from the public IP of your connection. You do not send location from the client. See [Cluster location](../provider/cluster-location.md).

You can also check status locally anytime (these URLs stay on your machine):

- Open **http://localhost:8080/** in a browser (or whatever port you configured) for a live status page.
- **GET /api/status** — JSON snapshot of that page.
- **GET /api/health** — full health snapshot.
- **GET /api/busy** — `{"busy": true}` or `{"busy": false}`.

On Linux or Windows with NVIDIA, utilization above **20%** is treated as busy. On every platform, including macOS, the client also treats itself as busy while an inference request is already in flight (default one at a time). Follow-up turns for the same session can wait up to **90 seconds** for that slot instead of getting **503**. See [Configuration](configuration.md).

## Model pricing and registration

**At startup**, the client discovers models on your local LLM server, looks up default prices from Inferoute, and registers each model for **that cluster**.

**Ongoing**, each health cycle checks for new models (for example after you re-run setup with a different approved model). New models are registered automatically — you do not need to restart the client.

Initial prices come from platform averages. You set your own prices **per cluster** in the dashboard — see [Model pricing](../provider/model-pricing.md).

Approved marketplace models must match a platform [approved model build](approved-models.md). The public catalog’s **`engines`** field lists which client (vLLM, vLLM Metal, llama.cpp) may serve that row. The client verifies the weight fingerprint against platform records before registration, health reporting, and inference.

The client reads the served model id and finds weights in the HuggingFace hub cache using the approved `hf_ref`. Optional **`hf_hub_cache`** or **`model_path`** only if your layout is non-standard.

## Inference requests

When Inferoute sends a request to your cluster:

1. **Authentication** — Valid requests include a signed header; missing or invalid requests get **401 Unauthorized**.
2. **Busy** — If the GPU is busy, or the client is already running an inference request, it responds with **503 Service Unavailable** so Inferoute can try another provider. Same-session follow-ups can wait for the slot instead.
3. **Proxy** — Valid requests are forwarded to your local LLM server. The client supports OpenAI-compatible **POST /v1/chat/completions** and **POST /v1/completions**.

## Connection to Inferoute

On startup the client opens an outbound connection to Inferoute. If the connection drops, the client reconnects on its own. You do not configure a public URL.

On shutdown, the client closes the connection.

## Related

- [Setup wizard](setup.md)
- [Configuration](configuration.md)
- [FAQ](faq.md)
- [Model pricing](../provider/model-pricing.md)
