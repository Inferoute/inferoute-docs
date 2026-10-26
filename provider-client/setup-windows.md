# Setup: Windows

Use this guide when you run the provider client natively on 64-bit Windows. For example, **inferoute-cluster1** on a Windows PC. The [setup wizard](setup.md) installs **llama.cpp**. Native vLLM is not supported.

You need an NVIDIA GPU with at least **24 GB** of VRAM, and **32 GB** of RAM plus a **fixed 64 GB page file** (or **64 GB** of RAM).

## Quick install (recommended)

1. Get your provider API key — see [Sign up and create a cluster](../getting-started/signup.md).
2. In **PowerShell**:

   ```powershell
   irm https://raw.githubusercontent.com/inferoute/inferoute-client/main/scripts/windows-install.ps1 | iex
   ```

The script installs **inferoute-client** to `%LOCALAPPDATA%\inferoute\bin`, runs **`inferoute-client setup`**, and adds that folder to your user **PATH**. It does **not** require Administrator. Re-run the wizard anytime:

```powershell
inferoute-client setup
```

The wizard downloads llama.cpp (CUDA) into `%LOCALAPPDATA%\inferoute\llama.cpp` and serves on `http://127.0.0.1:8081`. First-run download can take several minutes.

{% hint style="info" %}
Windows setup only lists catalog models that have a llama.cpp build. Fitting VRAM is not enough — encoder and embedding models such as `baai/bge-m3` stay off Windows. See [Approved model builds](approved-models.md).
{% endhint %}

3. Start the client from **Start Menu → Inferoute → Inferoute Client**, or from a **new** terminal:

   ```powershell
   inferoute-client
   ```

On Windows the client runs in the **notification area** by default. The terminal waits until the local dashboard is up, then the prompt returns. Closing that window does **not** stop the client.

Right-click the Inferoute icon and choose **Open dashboard** to see live status in your browser — session, models, GPU, and recent requests. **Open config** and **Open logs** open those files. Choose **Quit** on that menu when you want to stop the client.

To use the terminal dashboard instead:

```powershell
inferoute-client --console
```

Default config: `%USERPROFILE%\.config\inferoute\config.yaml`. Logs: `%USERPROFILE%\.local\state\inferoute\log`.

If startup fails with **Invalid provider API key**, copy the key from **My Clusters** → **Settings** into `api_key` and restart. See [FAQ](faq.md#the-client-failed-to-start-because-of-the-api-key).

If SmartScreen says **Windows protected your PC**, choose **More info** → **Run anyway**. The GitHub binary is not code-signed.

## GPU monitoring

Install the [NVIDIA driver](https://www.nvidia.com/drivers) so `nvidia-smi` is on **PATH**. You need **24 GB** of VRAM, and **32 GB** of RAM plus a **fixed 64 GB page file** (or **64 GB** of RAM). Then the health report includes GPU name, VRAM, and utilization. Utilization above **20%** marks the GPU busy in that report. **GET /api/busy** and a **503** follow inference slots. See [How it works](how-it-works.md). Without `nvidia-smi` the client still runs; GPU fields are empty, the health report does not mark the GPU busy from utilization, and the wizard will not offer llama.cpp.

`inferoute-client compatibility` uses the same `nvidia-smi` data, or system RAM if no NVIDIA GPU is present.

## Manual install

1. Download `inferoute-client-windows-amd64.zip` from [GitHub Releases](https://github.com/inferoute/inferoute-client/releases).
2. Place `inferoute-client.exe` on **PATH**.
3. Run `inferoute-client setup`.
4. Run `inferoute-client`.

The client opens an outbound connection to Inferoute — no ngrok or open firewall ports required.

## Related

- [Setup wizard](setup.md)
- [Installation](../getting-started/installation.md)
- [Configuration](configuration.md)
- [Setup: Linux](setup-linux.md)
- [FAQ](faq.md)
