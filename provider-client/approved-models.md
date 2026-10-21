# Approved model builds

Inferoute maintains a platform allowlist of **approved model builds**. The Provider Client uses this list to know which models can be offered on the marketplace and verifies your local weights against platform records.

Verification secrets (fingerprints and manifests) are **not** published. The client measures your local model and the platform returns **verified** or **failed**.

## Public catalog

**GET** `/api/models/approved-builds`

No authentication. Returns only **active** builds and **no hashes**. Safe sizing metadata such as `min_size_bytes` may be included so the client can run a local [compatibility check](compatibility.md). Each row also lists **`engines`**: which client runtimes may serve that build. Fingerprints and manifests are never returned.

Optional query:

| Parameter | Values | Purpose |
|-----------|--------|---------|
| `service_type` | `vllm` or `llama_cpp` | Filter by backend. Leave it out to get one row per alias |

### Example

```bash
curl -s https://core.inferoute.com/api/models/approved-builds | jq .
```
### Example response

```json
{
  "object": "list",
  "data": [
    {
      "id": "b2c8e1f0-1234-5678-9abc-def012345678",
      "alias": "Qwen/Qwen2.5-7B-Instruct",
      "service_type": "vllm",
      "hf_repo": "Qwen/Qwen2.5-7B-Instruct",
      "hf_ref": "main",
      "min_size_bytes": 15242760416,
      "is_active": true,
      "tool_call_parser": "hermes",
      "max_model_len": 131072,
      "rope_type": "yarn",
      "rope_base_context_len": 32768,
      "kv_cache_bytes_per_token": 57344,
      "engines": ["vllm", "vllm-metal"]
    },
    {
      "id": "c3d9f2a1-2345-6789-abcd-ef1234567890",
      "alias": "openai/gpt-oss-20b",
      "service_type": "vllm",
      "hf_repo": "openai/gpt-oss-20b",
      "hf_ref": "main",
      "min_size_bytes": 14000000000,
      "is_active": true,
      "tool_call_parser": "openai",
      "max_model_len": 131072,
      "rope_type": "yarn",
      "rope_base_context_len": 4096,
      "kv_cache_bytes_per_token": 24576,
      "engines": ["vllm", "vllm-metal"]
    }
  ]
}
```

| Field | Meaning |
|-------|---------|
| `alias` | Model id to use with Inferoute and your LLM server |
| `service_type` | `vllm` or `llama_cpp`. vLLM Metal uses `vllm` |
| `hf_repo` | HuggingFace repo id (`org/name`) for vLLM downloads |
| `hf_ref` | Branch or tag to download (for example `main`) |
| `min_size_bytes` | Measured minimum model weight size used by the local compatibility check |
| `engines` | Client runtimes allowed to serve this build: `vllm`, `vllm-metal`, `llama_cpp` |
| `gguf_file` | llama.cpp filename, for example `qwen2.5-7b-instruct-q4_k_m.gguf`. Present on `llama_cpp` rows |
| `tool_call_parser` | (vLLM optional) Parser name for auto tool calling, for example `hermes`. **Omit** (null) when the model does not support tools — setup and auto-start then start the engine **without** `--enable-auto-tool-choice` |
| `max_model_len` | (optional) Target context length the client must serve |
| `rope_type` | (vLLM optional) RoPE scaling type when YaRN is required, for example `yarn` |
| `rope_base_context_len` | (vLLM optional) Base context used to compute the YaRN factor |
| `kv_cache_bytes_per_token` | (vLLM optional) The model's exact KV cache cost per token, used by the local fit check |

**`engines` tells you which client can run the row.** It is not the same as `service_type`.

| If `engines` is… | Who sees the model |
|------------------|--------------------|
| missing or empty on a `vllm` row | **vLLM and vLLM Metal** |
| missing or empty on a `llama_cpp` row | **llama.cpp** |
| includes `llama_cpp` | Windows setup and `compatibility` will list it (if it also fits VRAM) |

The public catalog (no `service_type`) returns **one row per alias**. A Windows build is the same model: `engines` includes `llama_cpp` when that build exists. Encoder and embedding models (for example `baai/bge-m3`) stay off Windows even if they fit in VRAM.

Windows clients fetch `?service_type=llama_cpp`. That response includes `gguf_file`, the file llama.cpp serves. For example, `qwen2.5-7b-instruct-q4_k_m.gguf`.

Serve-flag fields are **nullable**. When set, setup and auto-start pass them into the engine:

| Engine | How `max_model_len` is applied | Tools / RoPE |
|--------|--------------------------------|--------------|
| **vLLM / vLLM Metal** | `--max-model-len N` | `--enable-auto-tool-choice --tool-call-parser …` when `tool_call_parser` is set; YaRN `--hf-overrides` when `rope_type` is set |
| **llama.cpp** | `-c N` | `--jinja` when `tool_call_parser` is set. RoPE fields are ignored |

Default product target for extended models is **128k** (`131072`). Ops can set a higher value per alias when VRAM allows.

The [setup wizard](setup.md) fetches this catalog, **drops rows this engine cannot serve**, scores the rest against this machine (including KV for `max_model_len` when set), and downloads the model you pick.

Health checks refresh this catalog to verify weights and live context. They do **not** rewrite `config.yaml` or restart the engine with new flags. If you start the engine yourself, match the catalog row — or re-run setup. See [Setup wizard](setup.md).

Consumers call Inferoute with the catalog **`alias`**. For example, `Qwen/Qwen2.5-7B-Instruct` routes to whichever providers are serving that alias, including Windows llama.cpp providers when `engines` includes `llama_cpp`.

## How verification works

1. Client fetches the **public catalog** (names, HuggingFace location, `engines`, and optional serve flags).
2. Client measures the local weight fingerprint.
3. Client calls **POST** `/api/provider/verify-model` with your **provider API key**.
4. Platform compares measurements to internal records and returns `verification_status`.
5. If the catalog lists `max_model_len`, the client also reads the **live** engine context. If it is lower than the catalog value (or unreadable), verification is **failed** and the model is not approved for routing.
6. Health reports include the verified status; the platform re-checks on ingest.

| Engine | Live context source |
|--------|---------------------|
| **vLLM / vLLM Metal** | `GET /v1/models` → model card `max_model_len` |
| **llama.cpp** | `GET /props` → `default_generation_settings.n_ctx` |

The client **caches** verify results for about **10 minutes** and only re-verifies when the catalog changes or your local model files change. The console UI reads the last health snapshot — it does not call verify on every refresh.

You do not need to call the verify API manually for day-to-day operation.

## Related

- [Setup wizard](setup.md)
- [Model compatibility check](compatibility.md)
- [How it works](how-it-works.md)
- [FAQ](faq.md)
