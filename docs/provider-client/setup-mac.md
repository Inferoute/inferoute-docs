# Setup: macOS (Apple GPU)

Use this guide when you run the provider client natively on a Mac with Apple Silicon. For example, **inferoute-cluster1** on a MacBook. You need **48 GB** of unified memory to run approved vLLM Metal models (BF16 7B-class). Intel Macs are not supported.

## Quick install (recommended)

1. Get your provider API key — see [Sign up and create a cluster](../getting-started/signup.md).
2. Run the install script:

   ```bash
   curl -fsSL https://raw.githubusercontent.com/inferoute/inferoute-client/main/scripts/install.sh | bash
   ```

The [setup wizard](setup.md) offers **vLLM Metal** or **llama.cpp**. It installs the one you pick if it is missing, and shows which models fit this Mac. vLLM Metal serves on `http://127.0.0.1:8000`. llama.cpp installs into `~/.local/share/inferoute/llama.cpp` and serves on `http://127.0.0.1:8081`. Re-run anytime:

```bash
inferoute-client setup
```

The script places **inferoute-client** in `/usr/local/bin`.

3. Start the client:

   ```bash
   inferoute-client
   ```

Default config: `~/.config/inferoute/config.yaml`. Logs: `~/.local/state/inferoute/log`.

## Apple GPU monitoring

On macOS the client reports basic GPU information (model name and core count). It does **not** report GPU memory or utilization, so load from other processes does not set the health-report busy flag. A request in flight still does (default one at a time). See [How it works](how-it-works.md).

On Linux with NVIDIA, the health report also marks the GPU busy when utilization is above **20%**. See [Setup: Linux](setup-linux.md).

## Manual install from source

1. **Go:** Install Go 1.22 or higher.
2. **Clone and build:**

   ```bash
   git clone https://github.com/inferoute/inferoute-client.git
   cd inferoute-client
   go build -o inferoute-client ./cmd
   ```

3. Run **`inferoute-client setup`**, then:

   ```bash
   ./inferoute-client --config ~/.config/inferoute/config.yaml
   ```

The client opens an outbound connection to Inferoute — no ngrok or open firewall ports required.

## Related

- [Setup wizard](setup.md)
- [Installation](../getting-started/installation.md)
- [Configuration](configuration.md)
- [Setup: Linux](setup-linux.md)
- [Setup: Windows](setup-windows.md)
- [FAQ](faq.md)
