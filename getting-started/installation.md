# Installation

Install the Inferoute Provider Client on the machine that will serve models. Confirm it meets [hardware requirements](requirements.md): **32 GB** of system memory and **24 GB** NVIDIA VRAM on Linux; on Windows **32 GB** of RAM plus a **fixed 64 GB page file** (or **64 GB** of RAM) and **24 GB** NVIDIA VRAM; or **48 GB** unified memory on a Mac. If you do not have a provider API key yet, complete [Sign up and create a cluster](signup.md) first.

The install script places the binary, then runs **`inferoute-client setup`**. See the [setup wizard](../provider-client/setup.md) for what it asks (engine, model, API key) and for non-interactive flags. Re-run anytime instead of editing YAML by hand:

```bash
inferoute-client setup
```

## Linux / macOS one-liner

Works on Linux (amd64/arm64) and macOS (Apple Silicon). Intel Macs are not supported.

```bash
curl -fsSL https://raw.githubusercontent.com/inferoute/inferoute-client/main/scripts/install.sh | bash
```

The wizard asks for your **provider API key** if you did not set `PROVIDER_API_KEY`.

The wizard installs:

| OS | Engine |
| --- | --- |
| **Linux** | vLLM (needs an NVIDIA GPU) |
| **macOS** | vLLM Metal (Apple Silicon) |

## Windows (PowerShell)

Requires 64-bit Windows. The wizard installs **llama.cpp**. Native vLLM is not supported. Windows only lists catalog rows tagged `llama_cpp`. Encoder and embedding models such as `baai/bge-m3` stay off Windows — see [Approved model builds](../provider-client/approved-models.md).

```powershell
irm https://raw.githubusercontent.com/inferoute/inferoute-client/main/scripts/windows-install.ps1 | iex
```

The script does not need Administrator. It installs `inferoute-client` to `%LOCALAPPDATA%\inferoute\bin`, runs setup, and adds a **Start Menu → Inferoute → Inferoute Client** shortcut. On Windows the client runs in the notification area by default — closing the terminal does not stop it. See [Setup: Windows](../provider-client/setup-windows.md).

You can also download `scripts/windows-install.bat` from the [inferoute-client](https://github.com/inferoute/inferoute-client) repo and double-click it (no administrator prompt).

## Skip the wizard (scripts / CI)

Set `INFEROUTE_SKIP_SETUP=1` and pass the old environment variables:

```bash
export INFEROUTE_SKIP_SETUP=1
export PROVIDER_API_KEY="your-provider-api-key"
export PROVIDER_TYPE="vllm"
export LLM_URL="http://127.0.0.1:8000"
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

If the client runs in Docker but the LLM engine runs on the host, set `LLM_URL` so the container can reach the host (for example `http://host.docker.internal:8000`). The engine on the host must listen on `0.0.0.0`, not only loopback.

The client listens on **127.0.0.1** inside the container. Inferoute reaches you through the outbound connection the client opens — you do not need to publish port 8080. To open the [local status dashboard](../provider-client/rest-api.md) from the host, set **server.host** to `0.0.0.0` and publish the port to loopback (for example `-p 127.0.0.1:8080:8080`). See [Configuration](../provider-client/configuration.md).

### Docker quick start

```bash
docker run -d \
  --name inferoute-client \
  -e PROVIDER_API_KEY="your-key" \
  -e PROVIDER_TYPE="vllm" \
  -e LLM_URL="http://host.docker.internal:8000" \
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
      - PROVIDER_TYPE=vllm
      - LLM_URL=http://host.docker.internal:8000
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

- [Setup wizard](../provider-client/setup.md)
- [Configuration](../provider-client/configuration.md)
- [Setup: Linux](../provider-client/setup-linux.md)
- [Setup: macOS](../provider-client/setup-mac.md)
- [Setup: Windows](../provider-client/setup-windows.md)
- [How it works](../provider-client/how-it-works.md)
