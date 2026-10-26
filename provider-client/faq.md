# FAQ

## General

### How do I get started as a provider?

1. Check [Software and hardware requirements](../getting-started/requirements.md).
2. [Sign up and create a cluster](../getting-started/signup.md) to copy a provider API key.
3. [Install the client](../getting-started/installation.md). The [setup wizard](setup.md) installs the LLM engine and an approved model — you do not need the engine already running.

### What is the Inferoute Provider Client?

A lightweight service that runs next to a local LLM engine (vLLM, vLLM Metal, or llama.cpp). It monitors GPU resources, reports health to Inferoute, and handles inference requests by forwarding them to that engine.

### What platforms are supported?

- **Linux with NVIDIA GPU:** At least **32 GB** of system memory and **24 GB** of VRAM. vLLM. The health report includes utilization. See [Setup: Linux](setup-linux.md).
- **macOS with Apple Silicon:** At least **48 GB** of unified memory for approved vLLM Metal models. The wizard also offers llama.cpp. Intel Macs are not supported. Basic GPU info (model, core count). Utilization is not reported. A request in flight still marks the client busy. See [Setup: macOS](setup-mac.md).
- **Windows amd64:** **32 GB** of RAM plus a **fixed 64 GB page file** (or **64 GB** of RAM), and an NVIDIA GPU with **24 GB** of VRAM. llama.cpp only. It only lists catalog rows tagged `llama_cpp`. Encoder and embedding models such as `baai/bge-m3` stay off Windows. GPU monitoring via `nvidia-smi` when the NVIDIA driver is installed. See [Setup: Windows](setup-windows.md).

### How do I configure the client?

Prefer **`inferoute-client setup`**. It writes engine, model, and API key into the YAML config. Server and logging stay in that file — see [Configuration](configuration.md). The connection to Inferoute is opened at startup — it is not a config section.

### How do I update the client?

Stop the client, run `inferoute-client update`, then start it again. The console tells you when a newer release exists. If it says **Update required**, Inferoute is no longer accepting that release. See [Updating the client](updating.md).

### How do I change engine or model later?

Stop the engine if it is already running, then re-run the [setup wizard](setup.md):

```bash
inferoute-client setup
```

Auto-start only replays `config.yaml`. It does not notice that the catalog row changed, and it will not restart a process that is already listening.

### Can I start vLLM (or llama.cpp) myself?

Yes. Use the same flags the wizard printed. If the catalog row has no `tool_call_parser`, do not pass `--enable-auto-tool-choice` (vLLM) or `--jinja` (llama.cpp). If the catalog sets `max_model_len`, serve at least that context or verification fails. Safer: let setup start the engine. See [Approved model builds](approved-models.md).

### How do I delete a cluster from my account?

Use the dashboard: **My Clusters** → select your cluster → **Settings** → **Delete cluster** (Danger zone). That stops inference and revokes API keys; it does not uninstall the client on your machine. See [Deleting and managing clusters](../provider/deleting-clusters.md).

## Model management

### When I add new models, does the client pick them up?

Yes. New models are detected on each health check (every **3 minutes**) and automatically registered with pricing from the Inferoute API. Marketplace routing still requires an [approved model build](approved-models.md). Re-run setup to switch the engine to a different approved model.

### How does model pricing work?

Pricing is **per cluster**, not account-wide. At startup the client discovers local models, applies default prices from Inferoute, and registers each model for **that cluster only**. For example, **inferoute-cluster1** and **inferoute-cluster2** can charge different prices for the same model name.

New models added later are registered during the next health cycle (every **3 minutes**).

See [Model pricing](../provider/model-pricing.md) for editing prices in the dashboard.

### Can I change model pricing after registration?

Yes. Open **My Clusters** → select a cluster → **Models** tab to edit prices for that cluster only. You can also call `PUT /api/provider/models/{model_id}` on `https://core.inferoute.com` with that cluster’s provider API key (per-token units). See [Model pricing](../provider/model-pricing.md).

Changing prices on one cluster does not change another. See [Model pricing](../provider/model-pricing.md).

### Which models are approved on the marketplace?

See [Approved model builds](approved-models.md). Call **GET** `/api/models/approved-builds` for the public catalog (aliases, HuggingFace locations, and `engines`; no hashes). The setup wizard lists rows tagged for this engine that should also fit this machine. On Windows, models without `llama_cpp` in `engines` are hidden. The dashboard says **No Windows build yet**.

## Reaching your machine

### Do I set a public URL?

No. On startup the client opens an outbound connection to Inferoute. Inferoute sends inference requests back over that connection. You do not install another program or open inbound ports.

### What if the connection drops?

The client reconnects on its own. Check the client logs if it stays disconnected.

### The client failed to start because of the API key

The provider API key in **config.yaml** (`api_key`) must be the key for **that cluster**, copied from **My Clusters** → select the cluster → **Settings**. A consumer key, an old rotated key, or a typo is rejected at startup with an “Invalid provider API key” message.

For example, after you regenerate the key in **Settings**, update `api_key` (or re-run setup) and restart the client. Do not leave the placeholder `your_api_key_here`.

## Health and monitoring

### How often does the client report health?

Every **3 minutes**, and also when the client becomes busy or free. It exposes **GET /api/health** for on-demand status.

### What is in a health report?

- GPU info (type, driver, CUDA, memory, utilization where available).
- Available models from the local LLM server.
- Whether the client is connected to Inferoute.

Cluster **country** is not part of the health payload. Inferoute resolves it from your connection. See [Cluster location](../provider/cluster-location.md).

### How is “GPU busy” determined?

There are two signals. See [How it works](how-it-works.md).

- **Rejecting a new request** (`GET /api/busy` and **503**): every inference slot is full. The default is one request at a time.
- **Health report** GPU `is_busy`: on Linux and Windows with NVIDIA, utilization above **20%**, or a slot is full. On macOS, a slot is full.
- No GPU monitoring and nothing in flight: the health report does not mark the GPU busy from utilization.

## Inference requests

### How are inference requests handled?

1. The request signature in `X-Request-Id` is validated with Inferoute; missing or invalid requests get **401**.
2. If every inference slot is full → **503** `{"error":"GPU is busy"}` (Inferoute can try another provider). Same-session follow-ups can wait for the slot instead. The **20%** utilization flag is on the health report.
3. Valid requests are proxied to the local LLM server.

### What endpoints does the client expose for inference?

OpenAI-compatible:

- **POST /v1/chat/completions**
- **POST /v1/completions**
- **POST /v1/embeddings**

## Logging

### How do I configure logging?

In `config.yaml`, under **logging:** set level (`debug`, `info`, `warn`, `error`), `log_dir`, `max_size`, `max_backups`, `max_age`. See [Configuration](configuration.md). Engine stdout from auto-start goes to **engine.log**.

### What if GPU monitoring isn’t available?

The client still runs. Health reports omit GPU utilization, so that flag stays clear unless a request is in flight. You can monitor activity via the console UI and log files.

## Related

- [Setup wizard](setup.md)
- [Installation](../getting-started/installation.md)
- [How it works](how-it-works.md)
- [Approved model builds](approved-models.md)
- [Model pricing](../provider/model-pricing.md)
- [Deleting and managing clusters](../provider/deleting-clusters.md)
