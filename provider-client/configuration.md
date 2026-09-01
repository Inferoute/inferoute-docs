# Configuration

The client reads a YAML config file. Default path is platform-specific (for example `~/.config/inferoute/config.yaml`). Override with `--config /path/to/config.yaml`.

## Sections

- **server** — HTTP server for the [local REST API](rest-api.md).
  - **port** — Listen port (default **8080**).
  - **host** — Bind address (default **127.0.0.1**). Leave this unless you need the status dashboard from another machine or from the Docker host. Set `0.0.0.0` only in that case, then publish the port to loopback on the host (for example `-p 127.0.0.1:8080:8080`). The Cloudflare tunnel does not need a published port.
  - **max_concurrent_inference** — How many inference requests the client will run at once (default **1**). Further requests return **503** so Inferoute can try another provider. Use **0** for unlimited. Follow-up turns for the same session can wait instead of getting 503 — see `session_queue_wait_seconds`.
  - **session_queue_wait_seconds** — How long a same-session follow-up waits for a free slot instead of **503** (default **90**).
  - **request_timeout_seconds** — HTTP read/write timeout. Must cover the session queue wait plus inference (default **240**).
- **provider** — Connection to the Inferoute platform.
  - **api_key** — Provider API key for **this cluster** (from **Clusters** → **Settings**). Required. The client refuses to start if this is empty or still `your_api_key_here`.
  - **url** — Inferoute platform URL. The install script sets `https://core.inferoute.com`. Do not point this at localhost unless you are developing against a local platform.
  - **provider_type** — `ollama` or `vllm`. Default: `ollama`.
  - **llm_url** — Local LLM API URL. For example, `http://localhost:11434` for Ollama or `http://localhost:8000` for vLLM. Default: `http://localhost:11434`.
  - **llm_timeout_seconds** — Timeout for requests forwarded to Ollama or vLLM (default **120**).
  - **hf_hub_cache** — (vLLM, optional) HuggingFace hub cache directory. Default: `~/.cache/huggingface/hub`. The client uses this to find weights for the model vLLM is serving.
  - **model_path** — (vLLM, optional) Flat directory override when you use `hf download --local-dir` instead of the hub cache layout.
- **logging**
  - **level** — `debug`, `info`, `warn`, `error`.
  - **log_dir** — Directory for log files (default: `~/.local/state/inferoute/log`).
  - **max_size** — Max log file size in MB before rotation (default: 100).
  - **max_backups** — Number of rotated files to keep (default: 5).
  - **max_age** — Max age of rotated files in days (default: 30).

Cloudflare Tunnel is **not** a config section. The client requests a tunnel from Inferoute at runtime. See [How it works](how-it-works.md).

## Log files

Under `log_dir` (default `~/.local/state/inferoute/log`):

- **inferoute.log** — Main application log (all levels).
- **error.log** — Error-level entries only.

## Overriding defaults

Defaults (for example provider type, LLM URL, server port) can be overridden without editing the config file.

**Install script (Linux/macOS):**

```bash
curl -fsSL https://raw.githubusercontent.com/inferoute/inferoute-client/main/scripts/install.sh | \
  PROVIDER_API_KEY="your-key" \
  PROVIDER_TYPE="vllm" \
  LLM_URL="http://localhost:8000" \
  SERVER_PORT="9090" \
  bash
```

**Install script (Windows):**

```powershell
$env:PROVIDER_API_KEY="your-key"
$env:PROVIDER_TYPE="ollama"
$env:LLM_URL="http://localhost:11434"
$env:SERVER_PORT="9090"
irm https://raw.githubusercontent.com/inferoute/inferoute-client/main/scripts/windows-install.ps1 | iex
```

Windows providers should use Ollama. See [Setup: Windows](setup-windows.md).

**Docker:**

Use `host.docker.internal` when the LLM runs on the host. The process binds **127.0.0.1** by default, so publishing a port does nothing until you set `server.host` to `0.0.0.0` (in the mounted config, not via env):

```bash
docker run -e PROVIDER_API_KEY="your-key" \
  -e PROVIDER_TYPE="ollama" \
  -e LLM_URL="http://host.docker.internal:11434" \
  inferoute/inferoute-client:latest
```

To open the local dashboard from the host, mount a config with `server.host: 0.0.0.0` and publish to loopback (`-p 127.0.0.1:8080:8080`). The tunnel does not need that port.

## Related

- [Installation](../getting-started/installation.md)
- [How it works](how-it-works.md)
- [Setup: Windows](setup-windows.md)
- [FAQ](faq.md)
