# Configuration

The client reads a YAML config file. Default path is platform-specific (for example `~/.config/inferoute/config.yaml`). Override with `--config /path/to/config.yaml`.

Prefer **`inferoute-client setup`** to change engine, model, or API key — see the [setup wizard](setup.md). Re-running the wizard updates this file and leaves server/logging settings in place.

## Sections

- **server** — HTTP server for the [local REST API](rest-api.md).
  - **port** — Listen port (default **8080**).
  - **host** — Bind address (default **127.0.0.1**). Leave this unless you need the status dashboard from another machine or from the Docker host. Set `0.0.0.0` only in that case, then publish the port to loopback on the host (for example `-p 127.0.0.1:8080:8080`). The connection to Inferoute does not need a published port.
  - **max_concurrent_inference** — How many inference requests the client will run at once. Leave this line out and the client measures a limit. See [Measuring concurrency](#measuring-concurrency). Set a number only when you want to override that measurement. **0** means unlimited. Further requests return **503** so Inferoute can try another provider. Follow-up turns for the same session can wait instead of getting 503 — see `session_queue_wait_seconds`. A measurement is the exception: every request gets **503** while it runs, including a same-session follow-up.
  - **session_queue_wait_seconds** — How long a same-session follow-up waits for a free slot instead of **503** (default **90**).
  - **request_timeout_seconds** — HTTP read/write timeout. Must cover the session queue wait plus inference (default **240**).
- **provider** — Connection to the Inferoute platform.
  - **api_key** — Provider API key for **this cluster** (from **My Clusters** → **Settings**). Required. The client refuses to start if this is empty or still `your_api_key_here`.
  - **url** — Inferoute platform URL. The install script sets `https://core.inferoute.com`. Do not point this at localhost unless you are developing against a local platform.
  - **provider_type** — `vllm` or `llama_cpp`. vLLM Metal uses `vllm`. llama.cpp uses `llama_cpp`.
  - **engine** — Local program: `vllm`, `vllm-metal`, or `llama_cpp`. Change this with `inferoute-client setup`.
  - **engine_bin** — Absolute path to the engine binary when it is not on `PATH` (typical for llama.cpp on Windows).
  - **model** — Catalog alias the client auto-starts (for example `Qwen/Qwen2.5-7B-Instruct`).
  - **auto_start** — When **true**, start the engine if **llm_url** is down, using the serve flags already in this file. Setup turns this on when it can find the binary on a single-node machine. On a multi-node master or worker, setup leaves this off. Existing configs without this field stay off. A process already bound on **llm_url** is not restarted.
  - **llm_url** — Local LLM API URL. For example, `http://127.0.0.1:8000` for vLLM and vLLM Metal, or `http://127.0.0.1:8081` for llama.cpp.
  - **gguf_file** — llama.cpp filename from the catalog, for example `qwen2.5-7b-instruct-q4_k_m.gguf`. Setup writes this.
  - **llm_timeout_seconds** — Timeout for requests forwarded to the local engine (default **120**).
  - **hf_hub_cache** — (vLLM, optional) HuggingFace hub cache directory. Default: `~/.cache/huggingface/hub`. The client uses this to find weights for the model vLLM is serving.
  - **model_path** — (vLLM, optional) Flat directory override when you use `hf download --local-dir` instead of the hub cache layout.
  - **hf_repo** — (optional) HuggingFace id when it differs from **model**. Setup writes this from the catalog.
  - **tool_call_parser**, **max_model_len**, **rope_type**, **rope_base_context_len** — (optional) Catalog serve flags copied by setup so auto-start can rebuild the same engine command offline. Missing `tool_call_parser` means start **without** tool calling. Changing these by hand does not update a running engine — stop it and re-run setup. See [Approved model builds](approved-models.md).
- **logging**
  - **level** — `debug`, `info`, `warn`, `error`.
  - **log_dir** — Directory for log files (default: `~/.local/state/inferoute/log`).
  - **max_size** — Max log file size in MB before rotation (default: 100).
  - **max_backups** — Number of rotated files to keep (default: 5).
  - **max_age** — Max age of rotated files in days (default: 30).

The connection to Inferoute is **not** a config section. The client opens it at startup from `provider.url`. See [How it works](how-it-works.md).

## Measuring concurrency

Leave `max_concurrent_inference` out of `config.yaml`. On vLLM and vLLM Metal the client starts at **4** requests at once, then measures how many this GPU can hold. llama.cpp stays at **1** and is not measured. A multi-node worker is not measured. The master is.

The measurement tries **1**, **2**, **4**, **8**, and **16** requests at once and stops when a higher count is worse. The terminal and the status page set **Session Status** to `benchmarking` and show **Benchmark**:

- `benchmarking (1/5)` through `benchmarking (5/5)` while it runs. It can stop before 5.
- `verified for N concurrent` when it finishes. **N** is the new slot limit.

Inferoute skips this machine until that finishes. A request that still arrives gets **503** `{"error":"GPU is busy"}`. **GET /api/busy** stays the slot check. It does not flip to `true` only because a measurement is running.

The result is saved next to your config as `benchmark.json`. A restart reuses it. The client measures again when the model, the engine version, the weights, or the GPU changes.

If one request finishes but is too slow, the limit stays at **1**. If the run errors, the client uses the recommended concurrency for that model, or **4** when the catalog has none.

If the GPU is already busy, the measured limit can come out low. Wait until it is idle, then measure again.

To measure again while the client is running:

```bash
inferoute-client benchmark
```

The command prints `benchmark requested; the running client measures on its next check`. It does not start a second client. The running client picks it up within a few seconds.

If `max_concurrent_inference` is already in your config, the client uses that number and does not measure. Delete the line, restart, and it will measure.

## Log files

Under `log_dir` (default `~/.local/state/inferoute/log`):

- **inferoute.log** — Main application log (all levels).
- **error.log** — Error-level entries only.
- **engine.log** — Stdout/stderr from an auto-started vLLM, vLLM Metal, or llama.cpp process.
- **inferscaled.log** — inferscale daemon output on a multi-node machine. Read this when setup says `inferscaled did not answer`.

The terminal and the status page show status, the **Benchmark** line while a measurement is running or has finished, and inference requests. When something fails, read **error.log**.

## Overriding defaults

Prefer `inferoute-client setup` to change engine, model, and API key.

For non-interactive installs, set `INFEROUTE_SKIP_SETUP=1` and pass environment variables:

**Install script (Linux/macOS):**

```bash
curl -fsSL https://raw.githubusercontent.com/inferoute/inferoute-client/main/scripts/install.sh | \
  INFEROUTE_SKIP_SETUP=1 \
  PROVIDER_API_KEY="your-key" \
  PROVIDER_TYPE="vllm" \
  LLM_URL="http://127.0.0.1:8000" \
  SERVER_PORT="9090" \
  bash
```

**Install script (Windows):**

```powershell
$env:INFEROUTE_SKIP_SETUP="1"
$env:PROVIDER_API_KEY="your-key"
$env:PROVIDER_TYPE="llama_cpp"
$env:LLM_URL="http://127.0.0.1:8081"
$env:SERVER_PORT="9090"
irm https://raw.githubusercontent.com/inferoute/inferoute-client/main/scripts/windows-install.ps1 | iex
```

Windows uses llama.cpp. See [Setup: Windows](setup-windows.md).

**Docker:**

Use `host.docker.internal` when the LLM runs on the host. The process binds **127.0.0.1** by default, so publishing a port does nothing until you set `server.host` to `0.0.0.0` (in the mounted config, not via env):

```bash
docker run -e PROVIDER_API_KEY="your-key" \
  -e PROVIDER_TYPE="vllm" \
  -e LLM_URL="http://host.docker.internal:8000" \
  inferoute/inferoute-client:latest
```

To open the local dashboard from the host, mount a config with `server.host: 0.0.0.0` and publish to loopback (`-p 127.0.0.1:8080:8080`). The connection to Inferoute does not need that port.

## Related

- [Setup wizard](setup.md)
- [Installation](../getting-started/installation.md)
- [How it works](how-it-works.md)
- [Setup: Windows](setup-windows.md)
- [FAQ](faq.md)
