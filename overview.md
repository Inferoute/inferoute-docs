# What is Inferoute

Inferoute is a provider network and orchestration layer for running LLM inference. A central **orchestrator** (API at [core.inferoute.com](https://core.inferoute.com)) routes requests to providers that run the **Inferoute Provider Client** alongside Ollama or vLLM. The orchestrator exposes an **OpenAI-compatible API**, so you can use the same client libraries and prompts you use with OpenAI while your traffic is routed to your own or third-party providers.

- **Orchestrator:** Manages consumers, API keys, providers, and model pricing; routes chat/completion requests to healthy providers.
- **Provider client:** Lightweight Go service on each provider machine that reports health, registers models and pricing, and handles inference by proxying to the local Ollama or vLLM server.
- **API:** OpenAI-compatible endpoints (e.g. `/v1/chat/completions`, `/v1/completions`) so existing tooling and code work with minimal changes.

All API documentation is published as OpenAPI 3 from the orchestrator repo and is available in this GitBook space (API Reference section).
