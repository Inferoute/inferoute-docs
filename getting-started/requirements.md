# Software and hardware requirements

The Inferoute Provider Client does **not** run models itself. It sits next to an LLM server you already installed and forwards inference requests to it.

Install and start **Ollama** or **vLLM** on the same machine (or a URL the client can reach) **before** you install the Inferoute client. If nothing is listening on that URL, the client cannot serve traffic.

## LLM software (required)

Pick one backend and keep it running:

| Platform | Supported LLM backends |
| --- | --- |
| **Linux** | [Ollama](https://ollama.com) or [vLLM](https://docs.vllm.ai/en/stable/getting_started/quickstart.html) |
| **macOS** (Intel or Apple Silicon) | Ollama (typical). vLLM is uncommon on Mac. |
| **Windows** (64-bit) | Ollama. vLLM is not supported on native Windows. |

You also need **at least one model loaded**:

- **Ollama:** pull a model (for example `ollama pull qwen2.5:7b`) so the server lists it.
- **vLLM:** serve an [approved model](../provider-client/approved-models.md) (for example `Qwen/Qwen2.5-7B-Instruct`).

Install guides: [Setup: Ollama](../provider-client/setup-ollama.md), [Setup: vLLM](../provider-client/setup-vllm.md).

## Hardware

| Requirement | Detail |
| --- | --- |
| **NVIDIA GPU** (Linux and Windows) | At least **8 GB** of VRAM. Install the [NVIDIA driver](https://www.nvidia.com/drivers) so `nvidia-smi` is on `PATH`. The Inferoute install script does not install the driver. |
| **macOS** | Apple Silicon or Intel. The client reports basic GPU info; busy detection is not available. Use Linux + NVIDIA for production routing with accurate busy status. |
| **System RAM** | **24 GB+** recommended if you run larger models. |
| **Disk** | **100 GB+** free is a practical starting point for model weights. |

vLLM additionally expects a CUDA-capable NVIDIA GPU (CUDA **11.7** or later in typical installs). See the [vLLM install docs](https://docs.vllm.ai/en/latest/getting_started/installation/).

## Platforms the client runs on

| Platform | GPU monitoring | Typical LLM backend |
| --- | --- | --- |
| **Linux + NVIDIA** | Full via `nvidia-smi` | Ollama or vLLM |
| **macOS** (Intel or Apple Silicon) | Basic via `system_profiler` | Ollama |
| **Windows amd64** | `nvidia-smi` when the NVIDIA driver is installed | Ollama |

## What the Inferoute client adds

Once the LLM server is up, the client:

- Reports health and models to Inferoute
- Registers pricing for **your cluster**
- Proxies inference to your local Ollama or vLLM server
- Opens a **Cloudflare Tunnel** so Inferoute can reach you without inbound firewall ports

Next: [Sign up and create a cluster](signup.md).
