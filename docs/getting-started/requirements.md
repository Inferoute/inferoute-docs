# Software and hardware requirements

The Inferoute Provider Client does **not** run models itself. It sits next to an LLM server and forwards inference requests to it.

The [setup wizard](../provider-client/setup.md) installs that LLM server and an approved model. You still need the right hardware (especially an NVIDIA GPU for vLLM and llama.cpp on Windows). If nothing is listening on the configured URL and auto-start is off, the client cannot serve traffic.

## LLM software

The wizard installs one engine for this machine:

| Platform | Engine |
| --- | --- |
| **Linux** | vLLM (needs an NVIDIA GPU). Multi-node clusters use this path only. |
| **macOS** (Apple Silicon) | vLLM Metal or llama.cpp |
| **Windows** (64-bit) | llama.cpp |

Intel Macs are not supported.

**uv** on Linux for vLLM. Setup installs it to `~/.local/bin` if it is missing. No system Python is needed; uv downloads Python 3.12 for the vLLM environment. The NVIDIA driver is the only host requirement. You do not install a CUDA toolkit.

Windows does not serve every catalog model. It only serves rows whose `engines` include `llama_cpp`. Encoder and embedding models such as `baai/bge-m3` stay off Windows. See [Approved model builds](../provider-client/approved-models.md).

## Hardware

Approved builds are BF16 7B-class weights (~14 GB on disk).

| Requirement | Detail |
| --- | --- |
| **System memory** (Linux) | At least **32 GB** of RAM. Loading approved BF16 weights needs host memory in addition to GPU VRAM. |
| **System memory** (Windows) | **32 GB** of RAM plus a **fixed 64 GB page file**, or **64 GB** of RAM |
| **NVIDIA GPU** (Linux and Windows) | At least **24 GB** of VRAM (for example RTX 3090, RTX 4090). Install the [NVIDIA driver](https://www.nvidia.com/drivers) so `nvidia-smi` is on `PATH`. The Inferoute install script does not install the driver. |
| **macOS** | Apple Silicon with at least **48 GB** of unified memory. Intel Macs are not supported. The client reports basic GPU info. Utilization is not reported. A request in flight still marks the client busy. On Linux, the health report also marks the GPU busy above **20%** utilization. See [How it works](../provider-client/how-it-works.md). |
| **Disk** | **100 GB+** free is a practical starting point for model weights. |

vLLM and Windows llama.cpp expect a CUDA-capable NVIDIA GPU. The wizard will not offer them if `nvidia-smi` is missing.

## Platforms the client runs on

| Platform | GPU monitoring | Typical LLM backend |
| --- | --- | --- |
| **Linux + NVIDIA** | Full via `nvidia-smi` | vLLM |
| **macOS** (Apple Silicon) | Basic via `system_profiler` | vLLM Metal or llama.cpp |
| **Windows amd64** | `nvidia-smi` when the NVIDIA driver is installed | llama.cpp |

Linux multi-node clusters share a private network between the machines in that cluster. See [Multi-node networking](../provider/multi-node-networking.md).

## What the Inferoute client adds

Once the LLM server is up, the client:

- Reports health and models to Inferoute
- Registers pricing for **your cluster**
- Proxies inference to your local LLM server
- Opens an outbound connection so Inferoute can reach you without inbound firewall ports

Next: [Sign up and create a cluster](signup.md).
