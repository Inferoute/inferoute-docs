# Installation

Install the Inferoute Provider Client on the machine that already runs Ollama or vLLM. If you do not have a provider API key yet, complete [Sign up and create a cluster](signup.md) first.

## Prerequisites

- A running LLM server — see [Software and hardware requirements](requirements.md).
- Your **provider API key** from cluster **Settings** (or from the **Deploy a Cluster** wizard).

## Linux / macOS one-liner

Works on Linux (amd64/arm64) and macOS (Intel and Apple Silicon):

```bash
PROVIDER_API_KEY="your-key" curl -fsSL https://raw.githubusercontent.com/inferoute/inferoute-client/main/scripts/install.sh | bash
```

On macOS, the script installs `cloudflared` via Homebrew when available, otherwise it downloads the native binary for your architecture.

## Windows (PowerShell)

Requires 64-bit Windows. Ollama should already be installed.

```powershell
$env:PROVIDER_API_KEY="your-key"; irm https://raw.githubusercontent.com/inferoute/inferoute-client/main/scripts/windows-install.ps1 | iex
```

Optional: `$env:PROVIDER_TYPE="ollama"`, `$env:LLM_URL="http://localhost:11434"`, `$env:SERVER_PORT="8080"`.

The script does not need Administrator. It installs `cloudflared` and `inferoute-client` to `%LOCALAPPDATA%\inferoute\bin`, writes `%USERPROFILE%\.config\inferoute\config.yaml`, and adds a **Start Menu → Inferoute → Inferoute Client** shortcut that runs with `--tray`. See [Setup: Windows](../provider-client/setup-windows.md).

You can also download `scripts/windows-install.bat` from the [inferoute-client](https://github.com/inferoute/inferoute-client) repo and double-click it (no administrator prompt).

## Manual environment variables

```bash
export PROVIDER_API_KEY="your-provider-api-key"
export PROVIDER_TYPE="ollama"   # or "vllm"
export LLM_URL="http://localhost:11434"   # or "http://localhost:8000" for vLLM
export SERVER_PORT="8080"

curl -fsSL https://raw.githubusercontent.com/inferoute/inferoute-client/main/scripts/install.sh | bash
```

To override defaults (provider type, LLM URL, port), see [Configuration](../provider-client/configuration.md#overriding-defaults).

## Launch the client

**Default config:**

```bash
inferoute-client
```

**Custom config path:**

```bash
inferoute-client --config ~/.config/inferoute/config.yaml
```

On Windows, start from **Start Menu → Inferoute → Inferoute Client**, or run `inferoute-client --tray` to hide the console and use the notification area.

## Docker

The official image is **inferoute/inferoute-client** on Docker Hub.

If the client runs in Docker but Ollama/vLLM runs on the host, set `LLM_URL` so the container can reach the host (for example `http://host.docker.internal:11434`). Ollama must listen on `0.0.0.0` — see [Setup: Ollama](../provider-client/setup-ollama.md).

### Docker quick start

```bash
docker run -d \
  --name inferoute-client \
  -p 8080:8080 \
  -e PROVIDER_API_KEY="your-key" \
  -e PROVIDER_TYPE="ollama" \
  -e LLM_URL="http://host.docker.internal:11434" \
  inferoute/inferoute-client:latest
```

### Docker Compose

```yaml
version: '3.8'
services:
  inferoute-client:
    image: inferoute/inferoute-client:latest
    ports:
      - "8080:8080"
    environment:
      - PROVIDER_API_KEY=your-key
      - PROVIDER_TYPE=ollama
      - LLM_URL=http://host.docker.internal:11434
    restart: unless-stopped
```

### Build from source

```bash
docker build -t inferoute-client .
docker run -d \
  --name inferoute-client \
  -p 8080:8080 \
  -e PROVIDER_API_KEY="your-key" \
  inferoute-client
```

## After first run

When the client starts, it publishes your available models with default costs. Open [www.inferoute.com](https://www.inferoute.com), go to **Clusters** → your cluster → **Models**, and set prices you actually want — see [Model pricing](../provider/model-pricing.md).

## Related

- [Configuration](../provider-client/configuration.md)
- [Setup: Ollama](../provider-client/setup-ollama.md)
- [Setup: vLLM](../provider-client/setup-vllm.md)
- [Setup: Linux](../provider-client/setup-linux.md)
- [Setup: macOS](../provider-client/setup-mac.md)
- [Setup: Windows](../provider-client/setup-windows.md)
- [How it works](../provider-client/how-it-works.md)
