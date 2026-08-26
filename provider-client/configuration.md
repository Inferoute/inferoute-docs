# Configuration

The client reads a YAML config file. Default path is platform-specific (for example `~/.config/inferoute/config.yaml`). Override with `--config /path/to/config.yaml`.

## Sections

- **server** — HTTP server (port, host) for the local REST API.
  - **max_concurrent_inference** — How many inference requests the client will run at once (default **1**). Further requests return **503** so Inferoute can try another provider. Use **0** for unlimited.
- **provider** — Connection to the Inferoute platform.
  - **provider_type** — `ollama`, `vllm`, or (future) `exo-labs`, `llama.cpp`. Default: `ollama`.
  - **llm_url** — Local LLM API URL. For example, `http://localhost:11434` for Ollama or `http://localhost:8000` for vLLM. Default: `http://localhost:11434`.
  - **hf_hub_cache** — (vLLM, optional) HuggingFace hub cache directory. Default: `~/.cache/huggingface/hub`. The client uses this to find weights for the model vLLM is serving.
  - **model_path** — (vLLM, optional) Flat directory override when you use `hf download --local-dir` instead of the hub cache layout.
- **logging**
  - **level** — `debug`, `info`, `warn`, `error`.
  - **log_dir** — Directory for log files (default: `~/.local/state/inferoute/log`).
  - **max_size** — Max log file size in MB before rotation (default: 100).
  - **max_backups** — Number of rotated files to keep (default: 5).
  - **max_age** — Max age of rotated files in days (default: 30).

## Log files

Under `log_dir` (default `~/.local/state/inferoute/log`):

- **inferoute.log** — Main application log (all levels).
- **error.log** — Error-level entries only.

## Overriding defaults

Defaults (for example provider type, LLM URL, server port) can be overridden without editing the config file.

**Install script (Linux/macOS):**

```bash
curl -fsSL https://raw.githubusercontent.com/Inferoute/inferoute-client/main/scripts/install.sh | \
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

Use `host.docker.internal` when the LLM runs on the host:

```bash
docker run -e PROVIDER_API_KEY="your-key" \
  -e PROVIDER_TYPE="ollama" \
  -e LLM_URL="http://host.docker.internal:11434" \
  -e SERVER_PORT="9090" \
  -p 9090:9090 \
  inferoute/inferoute-client:latest
```

## Related

- [Installation](../getting-started/installation.md)
- [How it works](how-it-works.md)
- [Setup: Windows](setup-windows.md)
- [FAQ](faq.md)
