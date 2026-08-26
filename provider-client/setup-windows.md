# Setup: Windows

Use this guide when you run the provider client natively on 64-bit Windows with [Ollama](https://ollama.com). For example, **inferoute-cluster1** on a Windows PC with Ollama installed locally. vLLM is not supported on native Windows.

## Quick install (recommended)

1. Install [Ollama for Windows](https://ollama.com) and pull at least one model.
2. Get your provider API key — see [Sign up and create a cluster](../getting-started/signup.md).
3. In **PowerShell**:

   ```powershell
   $env:PROVIDER_API_KEY="your-key"; irm https://raw.githubusercontent.com/inferoute/inferoute-client/main/scripts/windows-install.ps1 | iex
   ```

The script installs **cloudflared** and **inferoute-client** to `%LOCALAPPDATA%\inferoute\bin`, writes config to `%USERPROFILE%\.config\inferoute\config.yaml`, and adds that folder to your user **PATH**. It does **not** require Administrator.

4. Start the client from **Start Menu → Inferoute → Inferoute Client**, or from a **new** terminal:

   ```powershell
   inferoute-client
   ```

On Windows the client runs in the **notification area** by default. The terminal prompt returns; closing that window does **not** stop the client. A notification appears when the client starts.

Right-click the Inferoute icon and choose **Open dashboard** to see live status in your browser — session, models, GPU, and recent requests. Choose **Quit** on that menu when you want to stop the client.

To use the terminal dashboard instead:

```powershell
inferoute-client --console
```

Default config: `%USERPROFILE%\.config\inferoute\config.yaml`. Logs: `%USERPROFILE%\.local\state\inferoute\log`.

If SmartScreen says **Windows protected your PC**, choose **More info** → **Run anyway**. The GitHub binary is not code-signed.

## Ollama on Windows

Ollama is the supported backend on Windows.

If you need Ollama to listen on all interfaces (for example when the client runs in Docker), see [Setup: Ollama](setup-ollama.md#windows). For a native install, `http://localhost:11434` is the default.

Allow Ollama through **Windows Firewall** if prompted. The Inferoute Cloudflare tunnel is outbound HTTPS and does not need an inbound port.

## GPU monitoring

Install the [NVIDIA driver](https://www.nvidia.com/drivers) so `nvidia-smi` is on **PATH**. Then the client reports GPU name, VRAM, and busy status (utilization above **20%**). Without `nvidia-smi` the client still runs; GPU fields are empty and busy is not detected.

`inferoute-client compatibility` uses the same `nvidia-smi` data, or system RAM if no NVIDIA GPU is present.

## Manual install

1. Download `inferoute-client-windows-amd64.zip` from [GitHub Releases](https://github.com/inferoute/inferoute-client/releases).
2. Install **cloudflared**: download `cloudflared-windows-amd64.exe` from [Cloudflare releases](https://github.com/cloudflare/cloudflared/releases), or run `winget install Cloudflare.cloudflared`.
3. Place both executables on **PATH**.
4. Copy `config.yaml.example` to `%USERPROFILE%\.config\inferoute\config.yaml` and set **api_key**, **provider_type** to `ollama`, and **llm_url**.
5. Run `inferoute-client`.

The client requests a Cloudflare tunnel from the platform and runs **cloudflared** for you — no ngrok or open firewall ports required.

## Related

- [Installation](../getting-started/installation.md)
- [Configuration](configuration.md)
- [Setup: Ollama](setup-ollama.md)
- [Setup: Linux](setup-linux.md)
- [FAQ](faq.md)
