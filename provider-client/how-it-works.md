# How It Works

This page explains what the Provider Client does on your machine and how it talks to Inferoute. For install steps, see [Installation](../getting-started/installation.md). For engine and model selection, see the [setup wizard](setup.md).

## Overview

The client runs alongside the local LLM engine the wizard installed (vLLM, vLLM Metal, or llama.cpp). It:

1. Reports health (GPU and models) to Inferoute on a schedule.
2. Registers models and initial pricing for **your cluster**.
3. Accepts inference requests from Inferoute and forwards them to your local LLM server.
4. Opens an outbound connection so Inferoute can reach you without open firewall ports. It reconnects if the connection drops.

On a multi-node cluster, setup also connects this machine as the master (provider API key) or as a worker (a `join-` code). Every machine in that cluster has to start the engine together. See [Multi-node clusters](../provider/multi-node-clusters.md).

If **auto_start** is on (the wizard turns this on when it finds the engine binary), the client starts that engine when `llm_url` is down. It uses the serve flags already in `config.yaml` (including whether tool calling is on). It will not start a second copy if the port is already in use, and it will not pull fresh catalog flags at runtime. Re-run [setup](setup.md) after you change model or catalog flags.

## Health monitoring

Every **3 minutes**, the client sends a health report to Inferoute. It also sends one when an inference slot fills or frees, after a **10 second** cooldown so a burst of requests does not flood the platform. The report includes:

- GPU type, memory, and utilization (on Linux and Windows with NVIDIA).
- Which models your local server exposes.
- A GPU busy flag. See **When the client is busy** below.

Inferoute detects **cluster country** from the public IP of your connection. You do not send location from the client. See [Cluster location](../provider/cluster-location.md).

You can also check status locally anytime (these URLs stay on your machine):

- Open **http://localhost:8080/** in a browser (or whatever port you configured) for a live status page.
- **GET /api/status** — JSON snapshot of that page.
- **GET /api/health** — full health snapshot, including the GPU busy flag.
- **GET /api/busy** — `{"busy": true}` or `{"busy": false}`. This is the slot check only. It does not read `nvidia-smi`.

## When the client is busy

The client keeps two busy signals. Each one answers a different question.

| Signal | Busy when |
| --- | --- |
| **New request** (`GET /api/busy`, and **503**) | Every inference slot is taken. The default is **1** slot (`max_concurrent_inference`). NVIDIA utilization is not used. |
| **Health report** GPU `is_busy` | On NVIDIA, utilization is above **20%**, or an inference slot is full. On macOS, only when a slot is full. |

A different session that arrives while a slot is full gets **503** `{"error":"GPU is busy"}`. Inferoute can try another provider. A follow-up for the same session waits up to **90 seconds** (`session_queue_wait_seconds`) for that slot instead of **503**.

Utilization above **20%** sets the GPU busy flag in the health report. That update waits for the **3 minute** report. A request already on the machine is rejected when a slot is full. Another program using the GPU can set the flag. After your own request finishes and the slot is free, the next request is accepted while `nvidia-smi` is still high for a moment.

See [Configuration](configuration.md).

## Model pricing and registration

**At startup**, the client discovers models on your local LLM server, looks up default prices from Inferoute, and registers each model for **that cluster**.

**Ongoing**, each health cycle checks for new models (for example after you re-run setup with a different approved model). New models are registered automatically — you do not need to restart the client.

Initial prices come from platform averages. You set your own prices **per cluster** in the dashboard — see [Model pricing](../provider/model-pricing.md).

Approved marketplace models must match a platform [approved model build](approved-models.md). The public catalog’s **`engines`** field lists which client (vLLM, vLLM Metal, llama.cpp) may serve that row. The client verifies the weight fingerprint against platform records before registration, health reporting, and inference.

The client reads the served model id and finds weights in the HuggingFace hub cache using the approved `hf_ref`. Optional **`hf_hub_cache`** or **`model_path`** only if your layout is non-standard.

## Inference requests

When Inferoute sends a request to your cluster:

1. **Authentication** — Valid requests include a signed header; missing or invalid requests get **401 Unauthorized**.
2. **Busy** — If every inference slot is full, and this is not a same-session follow-up, the client responds with **503** `{"error":"GPU is busy"}` so Inferoute can try another provider. Same-session follow-ups can wait for the slot instead.
3. **Proxy** — Valid requests are forwarded to your local LLM server. The client supports **POST /v1/chat/completions**, **POST /v1/completions**, and **POST /v1/embeddings**.

## Connection to Inferoute

On startup the client opens an outbound connection to Inferoute. If the connection drops, the client reconnects on its own. You do not configure a public URL.

On shutdown, the client closes the connection.

## Related

- [Setup wizard](setup.md)
- [Configuration](configuration.md)
- [FAQ](faq.md)
- [Model pricing](../provider/model-pricing.md)
