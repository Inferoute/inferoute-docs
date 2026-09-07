# Setup: macOS (Apple GPU)

Use this guide when you run the provider client natively on a Mac. For example, **inferoute-cluster1** on a MacBook. You need **48 GB** of unified memory to run approved vLLM Metal models (BF16 7B-class). Ollama GGUF models can run on less RAM, but Inferoute’s approved vLLM catalog assumes 48 GB.

## Quick install (recommended)

1. Get your provider API key — see [Sign up and create a cluster](../getting-started/signup.md).
2. Run the install script:

   ```bash
   curl -fsSL https://raw.githubusercontent.com/inferoute/inferoute-client/main/scripts/install.sh | bash
   ```

The [setup wizard](setup.md) asks which engine to use (**Ollama**, or **vLLM Metal** on Apple Silicon), installs it if needed, and shows which models fit this Mac. Re-run anytime:

```bash
inferoute-client setup
```

The script detects Intel and Apple Silicon Macs, installs **cloudflared** (via Homebrew when available, otherwise a native binary download), and places **inferoute-client** in `/usr/local/bin`.

3. Start the client:

   ```bash
   inferoute-client
   ```

Default config: `~/.config/inferoute/config.yaml`. Logs: `~/.local/state/inferoute/log`.

## Apple GPU monitoring

On macOS the client reports basic GPU information (model name and core count). It does **not** report GPU memory or utilization, so load from other processes is invisible. The client still reports **busy** while it is already running an inference request (default one at a time).

For production provider workloads with utilization-based busy detection, use **Linux with an NVIDIA GPU** — see [Setup: Linux](setup-linux.md).

## Manual install from source

1. **Go:** Install Go 1.22 or higher.
2. **Clone and build:**

   ```bash
   git clone https://github.com/inferoute/inferoute-client.git
   cd inferoute-client
   go build -o inferoute-client ./cmd
   ```

3. **cloudflared:** Install with Homebrew (`brew install cloudflared`) or download the matching binary from [Cloudflare releases](https://github.com/cloudflare/cloudflared/releases) (`cloudflared-darwin-amd64.tgz` for Intel, `cloudflared-darwin-arm64.tgz` for Apple Silicon).
4. Run **`inferoute-client setup`**, then:

   ```bash
   ./inferoute-client --config ~/.config/inferoute/config.yaml
   ```

The client requests a Cloudflare tunnel from the platform and runs **cloudflared** for you — no ngrok or open firewall ports required.

## Related

- [Setup wizard](setup.md)
- [Installation](../getting-started/installation.md)
- [Configuration](configuration.md)
- [Setup: Linux](setup-linux.md)
- [Setup: Windows](setup-windows.md)
- [FAQ](faq.md)
