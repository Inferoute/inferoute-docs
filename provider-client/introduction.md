# Provider Client Introduction

The **Inferoute Provider Client** is a lightweight service that runs next to Ollama or vLLM. It reports health to Inferoute, registers models and pricing for **your cluster**, and forwards inference requests to your local LLM server.

Start here if you have not installed yet:

1. [Software and hardware requirements](../getting-started/requirements.md)
2. [Sign up and create a cluster](../getting-started/signup.md)
3. [Installation](../getting-started/installation.md)

## What it does

- Report provider health (GPU, models) to the Inferoute orchestrator
- Register models and pricing with the platform
- Accept inference requests from the orchestrator and proxy them to your local LLM server
- Expose your machine securely via **Cloudflare Tunnel** so the platform can reach your server without opening firewall ports

## Exposing your machine via Cloudflare Tunnel (secure HTTPS)

The client **exposes your machine to the internet** so Inferoute can send inference requests to your local Ollama or vLLM server. This uses **Cloudflare Tunnel** (cloudflared):

- **Secure & HTTPS:** Traffic between the internet and your provider goes through Cloudflare over HTTPS. Your home IP and ports are not exposed; Cloudflare provides a stable, TLS-terminated URL that tunnels back to your machine.
- **Why we install cloudflared:** The install script installs the `cloudflared` binary so the client can run it automatically. When you start the client, it requests a tunnel from Inferoute, then starts and supervises cloudflared. You do not need to run or configure cloudflared yourself.
- **No open firewall ports** are required on your side; outbound HTTPS to Cloudflare is sufficient.

## After first run

The client publishes your available models and sets initial costs from platform averages. Log in to the dashboard and adjust model pricing per cluster if needed — see [Model pricing](../provider/model-pricing.md).

## Related

- [How it works](how-it-works.md)
- [Configuration](configuration.md)
- [Model pricing](../provider/model-pricing.md)
