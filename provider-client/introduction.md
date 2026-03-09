# Provider Client Introduction

The **Inferoute Provider Client** is a lightweight Go service that runs on Ollama or vLLM provider machines. It handles health monitoring, reporting to the central Inferoute platform, and inference request handling by forwarding requests to your local Ollama or vLLM server.

Future support is planned for exo-labs and llama.cpp.

## Purpose

- Report provider health (GPU, models) to the Inferoute orchestrator
- Register models and pricing with the platform
- Accept inference requests from the orchestrator and proxy them to your local LLM server
- Expose your machine securely via **Cloudflare Tunnel** so the platform can reach your server without opening firewall ports

## Requirements

- A user and provider set up on [Inferoute](https://core.inferoute.com). See [How to add a provider](https://github.com/inferoute/inferoute-client/blob/main/docs/provider.md).
- Ollama or vLLM running locally.
- **Linux with NVIDIA GPU:** `nvidia-smi` must be installed and on `PATH` (for GPU monitoring and busy-state detection). Install the [NVIDIA driver](https://www.nvidia.com/drivers) for your system; the install script does not install it.
- **After first run:** The client publishes your available models and sets initial costs from platform averages. Log in to the platform and adjust model pricing to your preference if needed.

## Exposing your machine via Cloudflare Tunnel (secure HTTPS)

The client **exposes your machine to the internet** so the Inferoute platform can send inference requests to your local Ollama or vLLM server. This is done securely using **Cloudflare Tunnel** (cloudflared):

- **Secure & HTTPS:** Traffic between the internet and your provider goes through Cloudflare’s network over HTTPS. Your home IP and ports are not exposed; Cloudflare provides a stable, TLS-terminated URL that tunnels back to your machine.
- **Why we install cloudflared:** The install script installs the `cloudflared` binary so the client can run it automatically. When you start the client, it requests a tunnel from the Inferoute platform, then starts and supervises the cloudflared process. You do not need to run or configure cloudflared yourself—the client manages the tunnel for you.
- **No open firewall ports** are required on your side; outbound HTTPS to Cloudflare is sufficient.
