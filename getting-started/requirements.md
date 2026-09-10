# Software and hardware requirements

The Inferoute Provider Client does **not** run models itself. It sits next to an LLM server and forwards inference requests to it.

The [setup wizard](../provider-client/setup.md) installs that LLM server and an approved model. You still need the right hardware (especially an NVIDIA GPU for vLLM and FreeToken). If nothing is listening on the configured URL and auto-start is off, the client cannot serve traffic.

## LLM software

The wizard offers one backend for this machine:

| Platform | Supported LLM backends |
| --- | --- |
| **Linux** | Ollama or vLLM |
| **macOS** (Apple Silicon) | Ollama or vLLM Metal |
| **macOS** (Intel) | Ollama |
| **Windows** (64-bit) | Ollama or FreeToken |

Do not convert FreeToken checkpoints to FTW if you want marketplace verification.

## Hardware

Approved vLLM / FreeToken / vLLM Metal builds are BF16 7B-class weights (~14 GB on disk). That is why the floor is higher than a quantized Ollama GGUF.

| Requirement | Detail |
| --- | --- |
| **System memory** (Linux and Windows) | At least **32 GB** of RAM. Loading approved BF16 weights needs host memory in addition to GPU VRAM. |
| **NVIDIA GPU** (Linux and Windows) | At least **24 GB** of VRAM (for example RTX 3090, RTX 4090). Install the [NVIDIA driver](https://www.nvidia.com/drivers) so `nvidia-smi` is on `PATH`. The Inferoute install script does not install the driver. |
| **macOS** | Apple Silicon with at least **48 GB** of unified memory. Intel Macs can run Ollama only. The client reports basic GPU info; utilization-based busy detection is not available (in-flight requests still mark the client busy). Use Linux + NVIDIA for production routing with utilization-based busy status. |
| **Disk** | **100 GB+** free is a practical starting point for model weights. |

vLLM expects a CUDA-capable NVIDIA GPU. The wizard will not offer vLLM or FreeToken if `nvidia-smi` is missing.

## Platforms the client runs on

| Platform | GPU monitoring | Typical LLM backend |
| --- | --- | --- |
| **Linux + NVIDIA** | Full via `nvidia-smi` | Ollama or vLLM |
| **macOS** (Apple Silicon) | Basic via `system_profiler` | Ollama or vLLM Metal |
| **Windows amd64** | `nvidia-smi` when the NVIDIA driver is installed | Ollama or FreeToken |

## What the Inferoute client adds

Once the LLM server is up, the client:

- Reports health and models to Inferoute
- Registers pricing for **your cluster**
- Proxies inference to your local LLM server
- Opens a **Cloudflare Tunnel** so Inferoute can reach you without inbound firewall ports

Next: [Sign up and create a cluster](signup.md).
