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

### Setup says this machine's GPU differs from the master

The worker GPU is not the same model as the master's. Setup prints the two names and continues. Inferoute tests clusters where every machine has the same GPU. Mixing can work, and it can also leave vLLM stuck during startup. Use the same GPU when you can. See [GPUs across nodes](../provider/multi-node-clusters.md#gpus-across-nodes).

### Setup wants to reinstall vLLM

The cluster owner set a **vLLM version** on **Nodes**, and this machine has a different release. Pipeline parallel needs the same vLLM on every machine. Say yes and setup removes `~/.venv-vllm` and installs the pinned release. That takes several minutes. Say no and setup leaves the old release in place. The cluster may not start. A worker install does not ask. `--yes` also accepts the reinstall.

### vLLM crashes at startup with "Could not find nvcc"

vLLM tried to compile the FlashInfer sampling kernel on first start. The machine has a GPU driver and no CUDA compiler (`nvcc`). Rented GPU boxes often look like this.

`download-kernels` installs other FlashInfer kernels. It does not install this sampler. Setup still runs it on a new install. If vLLM was already installed, run it once yourself:

```bash
~/.venv-vllm/bin/python -m flashinfer download-kernels
```

On a machine with no `nvcc`, put this in front of the command setup printed. Set it on every machine in the cluster:

```bash
VLLM_USE_FLASHINFER_SAMPLER=0
```

For example, `VLLM_USE_FLASHINFER_SAMPLER=0 vllm serve ...`. Sampling is a little slower.

A machine with the CUDA toolkit installed can compile the sampler instead, so skip the variable there. `nvcc --version` has to succeed for the user that starts vLLM. The first start takes longer. Later starts reuse the compiled sampler. On a GPU with compute capability **10.0** or higher, install CUDA **12.9** or newer. Check the toolkit with `nvcc --version`. The CUDA version printed by `nvidia-smi` is the driver.

On a multi-node cluster, one crashed machine leaves the others waiting. The master often logs `No available shared memory broadcast block found in 60 seconds` while a worker is still compiling, or stuck waiting for that machine. That line is the wait. The worker crash is the failure. Stop vLLM on every machine, then start the master, then the workers. See [Multi-node clusters](../provider/multi-node-clusters.md).

### Setup asks for my password (`inferscale0`)

Multi-node setup joins a private network. It prints `inferscale needs root to create inferscale0` and runs `sudo`. Enter your password. If the machine has no `sudo`, setup stops with `inferscale needs root to create inferscale0, and sudo is not installed`. This step runs on Linux only. See [How the machines connect](../provider/multi-node-clusters.md#how-the-machines-connect).

### "one-shot key was rejected" or "one-shot key was already used"

This machine is not on the private network, and the one-shot network key is gone.

On the **master**, run `inferoute-client setup` again. Setup requests a new key.

On a **worker**, the join code was spent. Ask the owner for a **New code**. Then run:

```bash
inferoute-client setup --api-key "join-…"
```

`inferoute-client setup` alone prints `This machine is already in the cluster.` and will not ask for the new code.

### Can I move a machine to another cluster?

Yes. Run `inferoute-client setup --api-key "…"` with the new cluster's join code or provider API key. Setup logs the machine out of the old private network before it joins the new one. You do not need to clean anything up by hand.

If the machine still shows the old address after setup, log out yourself and run setup again:

```bash
sudo /usr/local/libexec/inferoute/inferscale logout
inferoute-client setup --api-key "…"
```

### "the owner removed this machine from the cluster"

The owner clicked **Remove** on **Nodes**. Run `inferoute-client setup` and paste a new join code. See [Joining a cluster](../provider/joining-a-cluster.md).

### How do I delete a cluster from my account?

Use the dashboard: **My Clusters** → select your cluster → **Settings** → **Delete cluster** (Danger zone). That stops inference and revokes API keys; it does not uninstall the client on your machine. See [Deleting and managing clusters](../provider/deleting-clusters.md).

## Model management

### When I add new models, does the client pick them up?

Yes. New models are detected on each health check (every **3 minutes**). The client advertises at most 5 approved models per cluster. Marketplace routing still requires an [approved model build](approved-models.md). Re-run setup to switch the engine to a different approved model.

### How does model pricing work?

Pricing is **per cluster**, not account-wide. A new model starts at the market-average price. You can change it once the model shows **Verified**, whether or not it shows **Serving**. A model that shows **Not verified** keeps the market-average price. For example, **inferoute-cluster1** and **inferoute-cluster2** can charge different prices for the same model name.

The client advertises at most 5 approved models. Each model has a maximum price.

See [Model pricing](../provider/model-pricing.md) for editing prices in the dashboard.

### Can I change model pricing after registration?

Yes, once the model shows **Verified**. Open **My Clusters** → select a cluster → **Models** tab. You can also call `PATCH /api/provider/models/{model_id}/pricing` on `https://core.inferoute.com` with that cluster’s provider API key. Prices are $/1M with up to 2 decimals. See [Model pricing](../provider/model-pricing.md).

Changing prices on one cluster does not change another. See [Model pricing](../provider/model-pricing.md).

### Which models are approved on the marketplace?

See [Approved model builds](approved-models.md). Call **GET** `/api/models/approved-builds` for the public catalog (aliases, HuggingFace locations, and `engines`; no hashes). The setup wizard lists rows tagged for this engine that should also fit this machine. On Windows, models without `llama_cpp` in `engines` are hidden. The dashboard says **No Windows build yet**.

## Reaching your machine

### Do I set a public URL?

No. On startup the client opens an outbound connection to Inferoute. Inferoute sends inference requests back over that connection. You do not install another program or open inbound ports.

### What if the connection drops?

The client reconnects on its own. Check the client logs if it stays disconnected.

### Can other providers SSH into my machine?

No, not over the private network a multi-node cluster uses. Port **22** is blocked there. That does not mean the other node is safe. Only share a cluster with people you intend to share GPUs with. See [Multi-node networking](../provider/multi-node-networking.md).

### What can other nodes in a multi-node cluster reach?

vLLM on **8000**, llama.cpp on **8081**, the distributed setup port **29501**, ping, and TCP **32768–60999** for GPU-to-GPU traffic. SSH, typical web admin, and database ports stay closed. The high-port window is wide on purpose. A service you bind there on all interfaces is reachable by every node in the cluster. See [Multi-node networking](../provider/multi-node-networking.md).

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
- [Multi-node clusters](../provider/multi-node-clusters.md)
- [Joining a cluster](../provider/joining-a-cluster.md)
