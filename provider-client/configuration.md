# Configuration

The client reads a YAML config file. Default path is platform-specific (for example `~/.config/inferoute/config.yaml`). Override with `--config /path/to/config.yaml`.

Prefer **`inferoute-client setup`** to change engine, model, or API key — see the [setup wizard](setup.md). Re-running the wizard updates this file and leaves server/logging settings in place.

## Sections

- **server** — HTTP server for the [local REST API](rest-api.md).
  - **port** — Listen port (default **8080**).
  - **host** — Bind address (default **127.0.0.1**). Leave this unless you need the status dashboard from another machine or from the Docker host. Set `0.0.0.0` only in that case, then publish the port to loopback on the host (for example `-p 127.0.0.1:8080:8080`). The connection to Inferoute does not need a published port.
  - **max_concurrent_inference** — How many inference requests the client will run at once (default **1**). Further requests return **503** so Inferoute can try another provider. Use **0** for unlimited. Follow-up turns for the same session can wait instead of getting 503 — see `session_queue_wait_seconds`.
  - **session_queue_wait_seconds** — How long a same-session follow-up waits for a free slot instead of **503** (default **90**).
  - **request_timeout_seconds** — HTTP read/write timeout. Must cover the session queue wait plus inference (default **240**).
- **provider** — Connection to the Inferoute platform.
  - **api_key** — Provider API key for **this cluster** (from **My Clusters** → **Settings**). Required. The client refuses to start if this is empty or still `your_api_key_here`.
  - **url** — Inferoute platform URL. The install script sets `https://core.inferoute.com`. Do not point this at localhost unless you are developing against a local platform.
  - **provider_type** — `vllm` or `llama_cpp`. vLLM Metal uses `vllm`. llama.cpp uses `llama_cpp`.
  - **engine** — Local program: `vllm`, `vllm-metal`, or `llama_cpp`. Change this with `inferoute-client setup`.
  - **engine_bin** — Absolute path to the engine binary when it is not on `PATH` (typical for llama.cpp on Windows).
  - **model** — Catalog alias the client auto-starts (for example `Qwen/Qwen2.5-7B-Instruct`).
  - **auto_start** — When **true**, start the engine if **llm_url** is down, using the serve flags already in this file. Setup turns this on when it can find the binary. Existing configs without this field stay off. A process already bound on **llm_url** is not restarted.
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

## Log files

Under `log_dir` (default `~/.local/state/inferoute/log`):

- **inferoute.log** — Main application log (all levels).
- **error.log** — Error-level entries only.
- **engine.log** — Stdout/stderr from an auto-started vLLM, vLLM Metal, or llama.cpp process.

The terminal and the status page show status and inference requests only. When something fails, read **error.log**.

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
