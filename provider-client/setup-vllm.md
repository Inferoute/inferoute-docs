# Setup: vLLM

On **Linux**, install and run vLLM according to the official docs. You need an NVIDIA GPU with at least **24 GB** of VRAM:

[vLLM Quick Start](https://docs.vllm.ai/en/stable/getting_started/quickstart.html)

On **macOS Apple Silicon**, use [vLLM Metal](https://docs.vllm.ai/projects/vllm-metal/en/latest/) on a Mac with at least **48 GB** of unified memory. The setup wizard can run the official Metal install script into `~/.venv-vllm-metal`.

Configure the Provider Client with:

- **provider_type:** `vllm`
- **engine:** `vllm` (Linux) or `vllm-metal` (Mac)
- **llm_url:** Your vLLM server URL (for example `http://127.0.0.1:8000`)

`inferoute-client setup` writes these for you.

If the client runs in Docker and vLLM runs on the host, use `http://host.docker.internal:8000` for `llm_url`.

## Download and serve an approved model

Use the **pinned HuggingFace revision** from [approved model builds](approved-models.md) (`hf_repo` + `hf_ref`). Floating `main` may not match the approved fingerprint.

### Option A — HuggingFace hub cache (default)

For example, if vLLM downloads into the standard cache when you serve by repo id:

```bash
vllm serve Qwen/Qwen3-0.6B
```

The Provider Client reads the model id from vLLM (`GET /v1/models`), looks up the approved `hf_ref`, and fingerprints weights for models located in the default vLLM folder  (`~/.cache/huggingface/hub/`)

You can keep many models in the same hub folder; verification only runs for the model vLLM is serving.

If your cache is not in the default location, set **`hf_hub_cache`** in config (for example `/home/ubuntu/.cache/huggingface/hub`).

### Option B — explicit download directory

For example:

```bash
hf download Qwen/Qwen3-0.6B --revision <hf_ref from API> --local-dir ~/models/Qwen3-0.6B
vllm serve ~/models/Qwen3-0.6B --served-model-name Qwen/Qwen3-0.6B
```

Set **`model_path`** in the Provider Client config to that same directory (`~/models/Qwen3-0.6B`).

## Related

- [Approved model builds](approved-models.md)
- [Configuration](configuration.md)
- [Installation](../getting-started/installation.md)
