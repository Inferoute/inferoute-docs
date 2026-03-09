# Setup: vLLM

Install and run vLLM according to the official docs:

[vLLM Quick Start](https://docs.vllm.ai/en/stable/getting_started/quickstart.html)

Configure the Provider Client with:

- **provider_type:** `vllm`
- **llm_url:** Your vLLM server URL (e.g. `http://localhost:8000`)

If the client runs in Docker and vLLM runs on the host, use `http://host.docker.internal:8000` for `LLM_URL`.
