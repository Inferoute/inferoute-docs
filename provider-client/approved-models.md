# Approved model builds

Inferoute maintains a platform allowlist of **approved model builds**. The Provider Client uses this list to know which models can be offered on the marketplace and verifies your local weights against platform records.

Verification secrets (digests, fingerprints, manifests) are **not** published. The client measures your local model and the platform returns **verified** or **failed**.

## Public catalog

**GET** `/api/models/approved-builds`

No authentication. Returns only **active** builds and **no hashes**. Safe sizing metadata such as `min_size_bytes` may be included so the client can run a local [compatibility check](compatibility.md). Digests, fingerprints, and manifests are never returned.

Optional query:

| Parameter | Values | Purpose |
|-----------|--------|---------|
| `service_type` | `ollama` or `vllm` | Filter by backend |

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
      "id": "41a99b3a-d928-4580-9656-3508c0529148",
      "alias": "gguf/qwen3:0.6b",
      "service_type": "ollama",
      "min_size_bytes": 523456789,
      "is_active": true
    },
    {
      "id": "b2c8e1f0-1234-5678-9abc-def012345678",
      "alias": "Qwen/Qwen3-0.6B",
      "service_type": "vllm",
      "hf_repo": "Qwen/Qwen3-0.6B",
      "hf_ref": "main",
      "min_size_bytes": 1200000000,
      "is_active": true
    }
  ]
}
```

| Field | Meaning |
|-------|---------|
| `alias` | Model id to use with Inferoute and your LLM server |
| `hf_repo` | HuggingFace repo id (`org/name`) for vLLM downloads |
| `hf_ref` | Branch or tag to download (for example `main`) |
| `min_size_bytes` | Measured minimum model weight size used by the local compatibility check |
| `tool_call_parser` | (vLLM optional) Parser name for auto tool calling, for example `hermes` |
| `max_model_len` | (optional) Target context length the client must serve |
| `rope_type` | (vLLM optional) RoPE scaling type when YaRN is required, for example `yarn` |
| `rope_base_context_len` | (vLLM optional) Base context used to compute the YaRN factor |

Serve-flag fields are **nullable**. When set, setup and auto-start pass them into the engine:

| Engine | How `max_model_len` is applied | Tools / RoPE |
|--------|--------------------------------|--------------|
| **vLLM / vLLM Metal** | `--max-model-len N` | `--enable-auto-tool-choice --tool-call-parser …` when `tool_call_parser` is set; YaRN `--hf-overrides` when `rope_type` is set |
| **FreeToken** | `--max-seq-len-override N` | Tools stay on FreeToken’s default (`auto`). RoPE fields are ignored |
| **Ollama** | Not applied | Unchanged |

Default product target for extended models is **128k** (`131072`). Ops can set a higher value per alias when VRAM allows.

The [setup wizard](setup.md) fetches this catalog, scores each build against this machine (including KV for `max_model_len` when set), and downloads the model you pick. You do not pull or serve models by hand.

Consumers call Inferoute with the catalog **`alias`**. For example, `gguf/qwen3:0.6b` routes to Ollama providers; `Qwen/Qwen3-0.6B` routes to vLLM / vLLM Metal / FreeToken providers.

## How verification works

1. Client fetches the **public catalog** (names, HuggingFace location, and optional serve flags).
2. Client hashes local model files (Ollama digest or vLLM weight files).
3. Client calls **POST** `/api/provider/verify-model` with your **provider API key**.
4. Platform compares measurements to internal records and returns `verification_status`.
5. For vLLM / FreeToken, if the catalog lists `max_model_len`, the client also reads the **live** engine context. If it is lower than the catalog value (or unreadable), verification is **failed** and the model is not approved for routing.
6. Health reports include the verified status; the platform re-checks on ingest.

| Engine | Live context source |
|--------|---------------------|
| **vLLM / vLLM Metal** | `GET /v1/models` → model card `max_model_len` |
| **FreeToken** | Prefer `/v1/models` `max_model_len` if present; else `GET /v1/cache/status` → `geometry.num_pages * geometry.page_size` |
| **Ollama** | No context gate in this release |

The client **caches** verify results for about **10 minutes** and only re-verifies when the catalog changes or your local model files change. The console UI reads the last health snapshot — it does not call verify on every refresh.

You do not need to call the verify API manually for day-to-day operation.

## Ollama vs vLLM

The same model family on Ollama and HuggingFace are **separate** catalog entries. Consumers pick the backend by model name (`gguf/...` → Ollama; bare HF id → vLLM).

| | Ollama | vLLM / Metal / FreeToken |
|---|--------|------|
| Example alias | `gguf/qwen3:0.6b` | `Qwen/Qwen3-0.6B` |
| Catalog fields | `alias` | `alias`, `hf_repo`, `hf_ref`, optional serve flags |
| Engine | `ollama` | `vllm`, `vllm-metal`, or `freetoken` |

## Related

- [Setup wizard](setup.md)
- [Model compatibility check](compatibility.md)
- [How it works](how-it-works.md)
- [FAQ](faq.md)
