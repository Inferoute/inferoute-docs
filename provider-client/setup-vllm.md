# Setup: vLLM

Install and run vLLM according to the official docs:

[vLLM Quick Start](https://docs.vllm.ai/en/stable/getting_started/quickstart.html)

Configure the Provider Client with:

- **provider_type:** `vllm`
- **llm_url:** Your vLLM server URL (for example `http://localhost:8000`)

If the client runs in Docker and vLLM runs on the host, use `http://host.docker.internal:8000` for `LLM_URL`.

Download weights at the **pinned HuggingFace revision** from [approved model builds](approved-models.md) (`hf_repo` + `hf_revision`). Floating `main` may not match the approved fingerprint.

For example, after fetching the allowlist:

```bash
hf download Qwen/Qwen2.5-7B-Instruct --revision <commit-sha> --local-dir ~/models/Qwen2.5-7B-Instruct
vllm serve ~/models/Qwen2.5-7B-Instruct --served-model-name Qwen/Qwen2.5-7B-Instruct
```

Set **`model_path`** in the Provider Client config to the same directory.

## Related

- [Configuration](configuration.md)
- [Installation](installation.md)
