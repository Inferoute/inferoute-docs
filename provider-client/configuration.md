# Configuration

The client reads a YAML config file. Default path is platform-specific (e.g. `~/.config/inferoute/config.yaml`). Override with `--config /path/to/config.yaml`.

## Sections

- **server** — HTTP server (port, host) for the local REST API.
- **provider** — Connection to the Inferoute platform.
  - **provider_type** — `ollama`, `vllm`, or (future) `exo-labs`, `llama.cpp`. Default: `ollama`.
  - **llm_url** — Local LLM API URL (e.g. `http://localhost:11434` for Ollama, `http://localhost:8000` for vLLM). Default: `http://localhost:11434`.
- **cloudflare** — Tunnel configuration.
  - **service_url** — Local URL to expose via the tunnel. Defaults to the provider’s `llm_url` if not set.
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

Defaults (e.g. provider type, LLM URL, server port) can be overridden without editing the config file.

**Install script (Linux/macOS):**

```bash
curl -fsSL https://raw.githubusercontent.com/Inferoute/inferoute-client/main/scripts/install.sh | \
  PROVIDER_API_KEY="your-key" \
  PROVIDER_TYPE="vllm" \
  LLM_URL="http://localhost:8000" \
  SERVER_PORT="9090" \
  bash
```

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
