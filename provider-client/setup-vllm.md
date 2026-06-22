# Setup: vLLM

Install and run vLLM according to the official docs:

[vLLM Quick Start](https://docs.vllm.ai/en/stable/getting_started/quickstart.html)

Configure the Provider Client with:

- **provider_type:** `vllm`
- **llm_url:** Your vLLM server URL (for example `http://localhost:8000`)

If the client runs in Docker and vLLM runs on the host, use `http://host.docker.internal:8000` for `llm_url`.

## Download and serve an approved model

Use the **pinned HuggingFace revision** from [approved model builds](approved-models.md) (`hf_repo` + `hf_revision`). Floating `main` may not match the approved fingerprint.

### Option A — HuggingFace hub cache (default)

For example, if vLLM downloads into the standard cache when you serve by repo id:

```bash
vllm serve Qwen/Qwen3-0.6B
```

The Provider Client **does not need a weight path** in config. It reads the model id from vLLM (`GET /v1/models`), looks up the approved `hf_revision`, and fingerprints weights under:

`~/.cache/huggingface/hub/models--Qwen--Qwen3-0.6B/snapshots/<hf_revision>/`

You can keep many models in the same hub folder; verification only runs for the model vLLM is serving.

If your cache is not in the default location, set **`hf_hub_cache`** in config (for example `/home/ubuntu/.cache/huggingface/hub`).

### Option B — explicit download directory

For example:

```bash
hf download Qwen/Qwen3-0.6B --revision <hf_revision from API> --local-dir ~/models/Qwen3-0.6B
vllm serve ~/models/Qwen3-0.6B --served-model-name Qwen/Qwen3-0.6B
```

Set **`model_path`** in the Provider Client config to that same directory (`~/models/Qwen3-0.6B`).

## Related

- [Approved model builds](approved-models.md)
- [Configuration](configuration.md)
- [Installation](installation.md)
