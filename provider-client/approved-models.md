# Approved model builds

Inferoute maintains a platform allowlist of **approved model builds**. The Provider Client uses this list to know which models can be offered on the marketplace and verifies your local weights against platform records.

Verification secrets (digests, fingerprints, manifests) are **not** published. The client measures your local model and the platform returns **verified** or **failed**.

## Public catalog

**GET** `/api/models/approved-builds`

No authentication. Returns only **active** builds and **no hashes**.

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
      "is_active": true
    },
    {
      "id": "b2c8e1f0-1234-5678-9abc-def012345678",
      "alias": "Qwen/Qwen3-0.6B",
      "service_type": "vllm",
      "hf_repo": "Qwen/Qwen3-0.6B",
      "hf_ref": "main",
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

## Ollama setup

```bash
curl -s "http://core.inferoute.com/api/models/approved-builds?service_type=ollama" | jq .
```

Use the **`alias`** when registering or calling Inferoute (for example `gguf/qwen3:0.6b`). Pull the model with Ollama, then start the Provider Client — it verifies the digest with the platform automatically.

## vLLM setup

```bash
curl -s "http://core.inferoute.com/api/models/approved-builds?service_type=vllm" | jq .
```

For each catalog entry:

1. Download **`hf_repo`** at **`hf_ref`** (for example `hf download Qwen/Qwen3-0.6B`).
2. Serve with **`alias`** as the model id.

For example:

```bash
vllm serve Qwen/Qwen3-0.6B
```

The Provider Client discovers weights from the HuggingFace hub cache using `hf_repo` and `hf_ref`. Optional **`model_path`** in config is only needed for flat directories from `hf download --local-dir`.

## How verification works

1. Client fetches the **public catalog** (names and HuggingFace location only).
2. Client hashes local model files (Ollama digest or vLLM weight files).
3. Client calls **POST** `/api/provider/verify-model` with your **provider API key**.
4. Platform compares measurements to internal records and returns `verification_status`.
5. Health reports include the verified status; the platform re-checks on ingest.

You do not need to call the verify API manually for day-to-day operation.

## Ollama vs vLLM

The same model family on Ollama and HuggingFace are **separate** catalog entries. Consumers pick the backend by model name (`gguf/...` → Ollama; bare HF id → vLLM).

| | Ollama | vLLM |
|---|--------|------|
| Example alias | `gguf/qwen3:0.6b` | `Qwen/Qwen3-0.6B` |
| Catalog fields | `alias` | `alias`, `hf_repo`, `hf_ref` |
| Download | `ollama pull qwen3:0.6b` | `hf download` at `hf_repo` |

## Related

- [Setup: Ollama](setup-ollama.md)
- [Setup: vLLM](setup-vllm.md)
- [How it works](how-it-works.md)
- [FAQ](faq.md)
