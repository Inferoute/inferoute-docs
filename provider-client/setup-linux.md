# Setup: Linux (NVIDIA)

Use this guide when you run the provider client natively on Linux with an NVIDIA GPU. For example, **inferoute-cluster1** on a workstation. You need at least **32 GB** of system memory and **24 GB** of VRAM for approved vLLM models.

## Quick install (recommended)

1. Get your provider API key — see [Sign up and create a cluster](../getting-started/signup.md).
2. Run the install script:

   ```bash
   curl -fsSL https://raw.githubusercontent.com/inferoute/inferoute-client/main/scripts/install.sh | bash
   ```

The [setup wizard](setup.md) installs **vLLM** if it is missing, and shows which models fit this GPU. Re-run anytime:

```bash
inferoute-client setup
```

3. Start the client:

   ```bash
   inferoute-client
   ```

Default config: `~/.config/inferoute/config.yaml`. Logs: `~/.local/state/inferoute/log`.

## NVIDIA GPU

- At least **32 GB** of system memory.
- At least **24 GB** of VRAM (for example RTX 3090 or RTX 4090).
- Install the [NVIDIA driver](https://www.nvidia.com/drivers) so `nvidia-smi` is on `PATH`. The wizard will not offer vLLM if `nvidia-smi` is missing.
- The client uses `nvidia-smi` for GPU name, VRAM, and utilization in the health report. Utilization above **20%** sets the GPU busy flag in that report. A new request is rejected when an inference slot is full. See [How it works](how-it-works.md).
- The install script does not install the driver.

## Blackwell GPUs

vLLM on a GPU with compute capability **10.0** or higher also needs FlashInfer kernels. That includes datacenter Blackwell and RTX 50-series. Hopper (**9.0**), Ada, and Ampere (for example an RTX 3090 or RTX 4090) skip this step.

When the wizard installs vLLM, it reads compute capability from `nvidia-smi`. At **10.0** or higher it prints **Blackwell GPU detected. Downloading FlashInfer kernels...** and downloads the kernels before setup continues. If that download fails, setup stops with **vLLM kernel download failed**.

If `nvidia-smi` cannot report compute capability, setup prints **Skipping vLLM kernel download** and continues. On a Blackwell GPU, vLLM can then fail to start. Fix `nvidia-smi`, then run `inferoute-client setup` again.

Check the value yourself:

```bash
nvidia-smi --query-gpu=compute_cap --format=csv,noheader
```

## Manual install from source

1. **Go:** Install Go 1.22 or higher.
2. **Clone and build:**

   ```bash
   git clone https://github.com/inferoute/inferoute-client.git
   cd inferoute-client
   go build -o inferoute-client ./cmd
   ```

3. Run **`inferoute-client setup`** so it writes config and can install the engine. Do not copy `config.yaml.example` by hand unless you are skipping the wizard on purpose.
4. **Run:**

   ```bash
   ./inferoute-client --config ~/.config/inferoute/config.yaml
   ```

The client opens an outbound connection to Inferoute. No need to install ngrok or open firewall ports.

## Related

- [Setup wizard](setup.md)
- [Installation](../getting-started/installation.md)
- [Configuration](configuration.md)
- [Setup: macOS](setup-mac.md)
- [Setup: Windows](setup-windows.md)
