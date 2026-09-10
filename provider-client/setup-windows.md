# Setup: Windows

Use this guide when you run the provider client natively on 64-bit Windows. For example, **inferoute-cluster1** on a Windows PC. The [setup wizard](setup.md) offers **Ollama** or **FreeToken**. Native vLLM is not supported. For approved BF16 models (FreeToken), you need at least **32 GB** of system memory and an NVIDIA GPU with at least **24 GB** of VRAM.

## Quick install (recommended)

1. Get your provider API key — see [Sign up and create a cluster](../getting-started/signup.md).
2. In **PowerShell**:

   ```powershell
   irm https://raw.githubusercontent.com/inferoute/inferoute-client/main/scripts/windows-install.ps1 | iex
   ```

The script installs **cloudflared** and **inferoute-client** to `%LOCALAPPDATA%\inferoute\bin`, runs **`inferoute-client setup`**, and adds that folder to your user **PATH**. It does **not** require Administrator. Re-run the wizard anytime:

```powershell
inferoute-client setup
```

The wizard can install Ollama (via winget) or the FreeToken **CLI** (`ft serve` on port **1919**) into `%LOCALAPPDATA%\inferoute\venv-freetoken`, including **CUDA PyTorch** (the Windows PyPI `torch` package is CPU-only and will not use your GPU). First-run download can take several minutes. This is not **FreeToken Desktop** — if that app is already installed, close it before setup. It uses the same API port, and its bundled `ft.exe` cannot serve models for Inferoute.

3. Start the client from **Start Menu → Inferoute → Inferoute Client**, or from a **new** terminal:

   ```powershell
   inferoute-client
   ```

On Windows the client runs in the **notification area** by default. The terminal prompt returns; closing that window does **not** stop the client. A notification appears when the client starts.

Right-click the Inferoute icon and choose **Open dashboard** to see live status in your browser — session, models, GPU, and recent requests. **Open config** and **Open logs** open those files. Choose **Quit** on that menu when you want to stop the client.

To use the terminal dashboard instead:

```powershell
inferoute-client --console
```

Default config: `%USERPROFILE%\.config\inferoute\config.yaml`. Logs: `%USERPROFILE%\.local\state\inferoute\log`.

If startup fails with **Invalid provider API key**, copy the key from **Clusters** → **Settings** into `api_key` and restart. See [FAQ](faq.md#the-client-failed-to-start-because-of-the-api-key).

If SmartScreen says **Windows protected your PC**, choose **More info** → **Run anyway**. The GitHub binary is not code-signed.

## GPU monitoring

Install the [NVIDIA driver](https://www.nvidia.com/drivers) so `nvidia-smi` is on **PATH**. You need at least **32 GB** of system memory and **24 GB** of VRAM for approved BF16 models. Then the client reports GPU name, VRAM, and busy status (utilization above **20%**). Without `nvidia-smi` the client still runs; GPU fields are empty, busy is not detected from utilization, and the wizard will not offer FreeToken.

`inferoute-client compatibility` uses the same `nvidia-smi` data, or system RAM if no NVIDIA GPU is present.

## Manual install

1. Download `inferoute-client-windows-amd64.zip` from [GitHub Releases](https://github.com/inferoute/inferoute-client/releases).
2. Install **cloudflared**: download `cloudflared-windows-amd64.exe` from [Cloudflare releases](https://github.com/cloudflare/cloudflared/releases), or run `winget install Cloudflare.cloudflared`.
3. Place both executables on **PATH**.
4. Run `inferoute-client setup`.
5. Run `inferoute-client`.

The client requests a Cloudflare tunnel from the platform and runs **cloudflared** for you — no ngrok or open firewall ports required.

## Related

- [Setup wizard](setup.md)
- [Installation](../getting-started/installation.md)
- [Configuration](configuration.md)
- [Setup: Linux](setup-linux.md)
- [FAQ](faq.md)
