# Provider Client Introduction

The **Inferoute Provider Client** is a lightweight service that runs next to a local LLM engine (vLLM, vLLM Metal, or FreeToken). It reports health to Inferoute, registers models and pricing for **your cluster**, and forwards inference requests to that engine.

The [setup wizard](setup.md) installs the engine if it is missing, picks an approved model that fits this machine, and writes config. You do not install the engine yourself.

Start here if you have not installed yet:

1. [Software and hardware requirements](../getting-started/requirements.md)
2. [Sign up and create a cluster](../getting-started/signup.md)
3. [Installation](../getting-started/installation.md)

## What it does

- Report provider health (GPU, models) to the Inferoute orchestrator
- Register models and pricing with the platform
- Accept inference requests from the orchestrator and proxy them to your local LLM server
- Open an outbound connection to Inferoute so the platform can send inference requests back to your machine without inbound firewall ports

## How Inferoute reaches your machine

The client opens an outbound connection to Inferoute when it starts, and reconnects if that connection drops. Inferoute sends inference requests back over that connection.

- You do not install another program.
- You do not open inbound firewall ports.
- You do not set a public URL. The address is the platform URL already in your config.

## After first run

The client publishes your available models and sets initial costs from platform averages. Log in to the dashboard and adjust model pricing per cluster if needed — see [Model pricing](../provider/model-pricing.md).

## Related

- [Setup wizard](setup.md)
- [How it works](how-it-works.md)
- [Configuration](configuration.md)
- [Model pricing](../provider/model-pricing.md)
