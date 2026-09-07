# Installation

Install the Inferoute Provider Client on the machine that will serve models. Confirm it meets [hardware requirements](requirements.md): **24 GB** NVIDIA VRAM on Linux or Windows, or **48 GB** unified memory on a Mac. If you do not have a provider API key yet, complete [Sign up and create a cluster](signup.md) first.

The install script places the binary, then runs **`inferoute-client setup`**. That wizard asks which inference program to use, can install it, shows which [approved models](../provider-client/approved-models.md) fit this machine, and writes `~/.config/inferoute/config.yaml`. Re-run the wizard anytime you want to change engine, model, or API key instead of editing YAML by hand:

```bash
inferoute-client setup
```

## Linux / macOS one-liner

Works on Linux (amd64/arm64) and macOS (Intel and Apple Silicon):

```bash
curl -fsSL https://raw.githubusercontent.com/inferoute/inferoute-client/main/scripts/install.sh | bash
```

The wizard asks for your **provider API key** if you did not set `PROVIDER_API_KEY`. On macOS, the script installs `cloudflared` via Homebrew when available, otherwise it downloads the native binary for your architecture.

Engine choices:

| OS | Wizard options |
| --- | --- |
| **Linux** | Ollama or vLLM |
| **macOS** | Ollama or vLLM Metal (Apple Silicon) |

## Windows (PowerShell)

Requires 64-bit Windows. The wizard offers **Ollama** or **FreeToken**.

```powershell
irm https://raw.githubusercontent.com/inferoute/inferoute-client/main/scripts/windows-install.ps1 | iex
```

The script does not need Administrator. It installs `cloudflared` and `inferoute-client` to `%LOCALAPPDATA%\inferoute\bin`, runs setup, and adds a **Start Menu → Inferoute → Inferoute Client** shortcut. On Windows the client runs in the notification area by default — closing the terminal does not stop it. See [Setup: Windows](../provider-client/setup-windows.md).

You can also download `scripts/windows-install.bat` from the [inferoute-client](https://github.com/inferoute/inferoute-client) repo and double-click it (no administrator prompt).

## Skip the wizard (scripts / CI)

Set `INFEROUTE_SKIP_SETUP=1` and pass the old environment variables:

```bash
export INFEROUTE_SKIP_SETUP=1
export PROVIDER_API_KEY="your-provider-api-key"
export PROVIDER_TYPE="ollama"   # or "vllm"
export LLM_URL="http://127.0.0.1:11434"   # or "http://127.0.0.1:8000" for vLLM
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

On Windows, start from **Start Menu → Inferoute → Inferoute Client**, or run `inferoute-client`. The client stays in the notification area; right-click the icon and choose **Open dashboard** for live status. Use `inferoute-client --console` if you want the terminal UI.

## Docker

The official image is **inferoute/inferoute-client** on Docker Hub.

If the client runs in Docker but Ollama/vLLM runs on the host, set `LLM_URL` so the container can reach the host (for example `http://host.docker.internal:11434`). Ollama must listen on `0.0.0.0` — see [Setup: Ollama](../provider-client/setup-ollama.md).

The client listens on **127.0.0.1** inside the container. Inferoute reaches you through the Cloudflare tunnel — you do not need to publish port 8080. To open the [local status dashboard](../provider-client/rest-api.md) from the host, set **server.host** to `0.0.0.0` and publish the port to loopback (for example `-p 127.0.0.1:8080:8080`). See [Configuration](../provider-client/configuration.md).

### Docker quick start

```bash
docker run -d \
  --name inferoute-client \
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
