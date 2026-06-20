# Setup: macOS (Apple GPU)

Use this guide when you run the provider client natively on a Mac with an Apple GPU. For example, **inferoute-cluster1** on a MacBook with Ollama installed locally.

## Quick install (recommended)

1. Install [Ollama](https://ollama.com) and pull at least one model.
2. Get your provider API key from the [Inferoute platform](https://core.inferoute.com).
3. Run the install script:

   ```bash
   PROVIDER_API_KEY="your-key" curl -fsSL https://raw.githubusercontent.com/inferoute/inferoute-client/main/scripts/install.sh | bash
   ```

The script detects Intel and Apple Silicon Macs, installs **cloudflared** (via Homebrew when available, otherwise a native binary download), and places **inferoute-client** in `/usr/local/bin`.

4. Start the client:

   ```bash
   inferoute-client
   ```

Default config: `~/.config/inferoute/config.yaml`. Logs: `~/.local/state/inferoute/log`.

## Ollama on macOS

Ollama is the typical backend on Mac. vLLM is not commonly used on macOS.

If you need Ollama to listen on all interfaces (for example when the client runs in Docker), see [Setup: Ollama](setup-ollama.md#macos-application).

## Apple GPU monitoring

On macOS the client reports basic GPU information (model name and core count). It does **not** report GPU memory or utilization, and **busy** is always reported as false. That means Inferoute may route requests to your Mac even when Ollama is already under load.

For production provider workloads with accurate busy detection, use **Linux with an NVIDIA GPU** — see [Setup: Linux](setup-linux.md).

## Manual install from source

1. **Go:** Install Go 1.21 or higher.
2. **Clone and build:**

   ```bash
   git clone https://github.com/sentnl/inferoute-client.git
   cd inferoute-client
   cp config.yaml.example config.yaml
   nano config.yaml   # set provider API key and other options
   go build -o inferoute-client ./cmd
   ```

3. **cloudflared:** Install with Homebrew (`brew install cloudflared`) or download the matching binary from [Cloudflare releases](https://github.com/cloudflare/cloudflared/releases) (`cloudflared-darwin-amd64.tgz` for Intel, `cloudflared-darwin-arm64.tgz` for Apple Silicon).
4. **Run:**

   ```bash
   ./inferoute-client --config config.yaml
   ```

The client requests a Cloudflare tunnel from the platform and runs **cloudflared** for you — no ngrok or open firewall ports required.

## Related

- [Installation](installation.md)
- [Configuration](configuration.md)
- [Setup: Ollama](setup-ollama.md)
- [Setup: Linux](setup-linux.md)
- [FAQ](faq.md)
