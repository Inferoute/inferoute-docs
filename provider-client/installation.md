# Installation

## Quick start (install script)

### Prerequisites

- Get your provider API key from the [Inferoute platform](https://core.inferoute.com).

### Linux / macOS one-liner

```bash
PROVIDER_API_KEY="your-key" curl -fsSL https://raw.githubusercontent.com/inferoute/inferoute-client/main/scripts/install.sh | bash
```

### Manual environment variables

```bash
export PROVIDER_API_KEY="your-provider-api-key"
export PROVIDER_TYPE="ollama"   # or "vllm"
export LLM_URL="http://localhost:11434"   # or "http://localhost:8000" for vLLM
export SERVER_PORT="8080"

curl -fsSL https://raw.githubusercontent.com/inferoute/inferoute-client/main/scripts/install.sh | bash
```

To override defaults (provider type, LLM URL, port), see [Configuration](configuration.md#overriding-defaults).

## Launching the client

**Default config:**

```bash
inferoute-client
```

**Custom config path:**

```bash
inferoute-client --config /path/to/config.yaml
```

## Docker

The official image is **inferoute/inferoute-client** on Docker Hub.

If the client runs in Docker but Ollama/vLLM runs on the host, set `LLM_URL` so the container can reach the host (e.g. `http://host.docker.internal:11434`). See [Setup: Ollama](setup-ollama.md) for making Ollama listen on `0.0.0.0`.

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
docker run -d --name inferoute-client -p 8080:8080 -e PROVIDER_API_KEY="your-key" inferoute-client
```

## After first run

When the client starts, it publishes your available models with default costs. Log in to [Inferoute](https://core.inferoute.com) and adjust model pricing if needed.
