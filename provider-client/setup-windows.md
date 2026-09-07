# Setup: Windows

Use this guide when you run the provider client natively on 64-bit Windows. For example, **inferoute-cluster1** on a Windows PC. The wizard offers **Ollama** or **FreeToken**. Native vLLM is not supported. For approved BF16 models (FreeToken), you need an NVIDIA GPU with at least **24 GB** of VRAM.

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

## Ollama on Windows

Ollama is one supported backend on Windows.

If you need Ollama to listen on all interfaces (for example when the client runs in Docker), see [Setup: Ollama](setup-ollama.md#windows). For a native install, `http://127.0.0.1:11434` is the default.

Allow Ollama through **Windows Firewall** if prompted. The Inferoute Cloudflare tunnel is outbound HTTPS and does not need an inbound port.

## FreeToken on Windows

[FreeToken](https://www.flashml.ai/) is an OpenAI-compatible server (`http://127.0.0.1:1919`). Set **provider_type** to `vllm` and **engine** to `freetoken`. Serve catalog HuggingFace repos (same weights as vLLM) so marketplace verification matches. Do not convert to FTW for Inferoute.

The wizard can download `FreeToken-Setup-win-x64.exe` and run it silent. Windows may still ask you to allow the installer.

## GPU monitoring

Install the [NVIDIA driver](https://www.nvidia.com/drivers) so `nvidia-smi` is on **PATH**. You need at least **24 GB** of VRAM for approved BF16 models. Then the client reports GPU name, VRAM, and busy status (utilization above **20%**). Without `nvidia-smi` the client still runs; GPU fields are empty and busy is not detected.

`inferoute-client compatibility` uses the same `nvidia-smi` data, or system RAM if no NVIDIA GPU is present.

## Manual install

1. Download `inferoute-client-windows-amd64.zip` from [GitHub Releases](https://github.com/inferoute/inferoute-client/releases).
2. Install **cloudflared**: download `cloudflared-windows-amd64.exe` from [Cloudflare releases](https://github.com/cloudflare/cloudflared/releases), or run `winget install Cloudflare.cloudflared`.
3. Place both executables on **PATH**.
4. Run `inferoute-client setup`, or copy `config.yaml.example` to `%USERPROFILE%\.config\inferoute\config.yaml` and set **api_key**, **engine**, and **llm_url**.
5. Run `inferoute-client`.

The client requests a Cloudflare tunnel from the platform and runs **cloudflared** for you — no ngrok or open firewall ports required.

## Related

- [Installation](../getting-started/installation.md)
- [Configuration](configuration.md)
- [Setup: Ollama](setup-ollama.md)
- [Setup: Linux](setup-linux.md)
- [FAQ](faq.md)
