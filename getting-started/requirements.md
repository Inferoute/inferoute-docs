# Software and hardware requirements

The Inferoute Provider Client does **not** run models itself. It sits next to an LLM server and forwards inference requests to it.

The install wizard can install the LLM server for you. You still need the right hardware (especially an NVIDIA GPU for vLLM and FreeToken). If nothing is listening on the configured URL and auto-start is off, the client cannot serve traffic.

## LLM software (required)

Pick one backend. Keep it running, or let the client auto-start it after setup:

| Platform | Supported LLM backends |
| --- | --- |
| **Linux** | [Ollama](https://ollama.com) or [vLLM](https://docs.vllm.ai/en/stable/getting_started/quickstart.html) |
| **macOS** (Apple Silicon) | Ollama or [vLLM Metal](https://docs.vllm.ai/projects/vllm-metal/en/latest/) |
| **macOS** (Intel) | Ollama |
| **Windows** (64-bit) | Ollama or [FreeToken](https://www.flashml.ai/) |

You also need **at least one model loaded**:

- **Ollama:** pull a model (for example `ollama pull qwen2.5:7b`) so the server lists it.
- **vLLM / vLLM Metal / FreeToken:** serve an [approved model](../provider-client/approved-models.md) from HuggingFace (for example `Qwen/Qwen2.5-7B-Instruct`). Do not convert FreeToken checkpoints to FTW if you want marketplace verification.

Install guides: [Setup: Ollama](../provider-client/setup-ollama.md), [Setup: vLLM](../provider-client/setup-vllm.md).

## Hardware

Approved vLLM / FreeToken / vLLM Metal builds are BF16 7B-class weights (~14 GB on disk). That is why the floor is higher than a quantized Ollama GGUF.

| Requirement | Detail |
| --- | --- |
| **NVIDIA GPU** (Linux and Windows) | At least **24 GB** of VRAM (for example RTX 3090, RTX 4090). Install the [NVIDIA driver](https://www.nvidia.com/drivers) so `nvidia-smi` is on `PATH`. The Inferoute install script does not install the driver. |
| **macOS** | Apple Silicon with at least **48 GB** of unified memory. Intel Macs can run Ollama only. The client reports basic GPU info; utilization-based busy detection is not available (in-flight requests still mark the client busy). Use Linux + NVIDIA for production routing with utilization-based busy status. |
| **Disk** | **100 GB+** free is a practical starting point for model weights. |

vLLM additionally expects a CUDA-capable NVIDIA GPU (CUDA **11.7** or later in typical installs). See the [vLLM install docs](https://docs.vllm.ai/en/latest/getting_started/installation/).

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
- Proxies inference to your local Ollama or vLLM server
- Opens a **Cloudflare Tunnel** so Inferoute can reach you without inbound firewall ports

Next: [Sign up and create a cluster](signup.md).
