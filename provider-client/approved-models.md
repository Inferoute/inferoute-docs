# Approved model builds

Inferoute maintains a platform allowlist of **approved model builds** — the exact Ollama digests and vLLM weight fingerprints that marketplace providers are expected to match. The Provider Client uses this list for model integrity verification (see rollout in platform updates).

You can read the allowlist yourself to see which models are approved and which HuggingFace revision to download for vLLM.

## List approved builds

**GET** `/api/models/approved-builds`

No authentication. Returns only **active** builds.

Optional query:

| Parameter | Values | Purpose |
|-----------|--------|---------|
| `service_type` | `ollama` or `vllm` | Filter by backend |

### Example: all approved builds

```bash
curl -s https://core.inferoute.com/api/models/approved-builds | jq .
```

Local development (via Traefik):

```bash
curl -s http://localhost/api/models/approved-builds | jq .
```

### Example response (mixed backends)

```json
{
  "object": "list",
  "data": [
    {
      "id": "41a99b3a-d928-4580-9656-3508c0529148",
      "alias": "gguf/qwen3:0.6b",
      "service_type": "ollama",
      "expected_digest": "7df6b6e09427a769808717c0a93cadc4ae99ed4eb8bf5ca557c90846becea435",
      "min_size_bytes": 522653767,
      "is_active": true
    },
    {
      "id": "b2c8e1f0-1234-5678-9abc-def012345678",
      "alias": "Qwen/Qwen3-0.6B",
      "service_type": "vllm",
      "weight_fingerprint": "a1b2c3d4e5f6...",
      "min_size_bytes": 1520000000,
      "hf_repo": "Qwen/Qwen3-0.6B",
      "hf_revision": "abc123def456...",
      "manifest": [
        {
          "name": "config.json",
          "sha256": "...",
          "hash_method": "full",
          "size": 1200
        },
        {
          "name": "model.safetensors",
          "sha256": "...",
          "hash_method": "safetensors_header",
          "size": 1500000000
        }
      ],
      "is_active": true
    }
  ]
}
```

### Ollama only

```bash
curl -s "http://localhost/api/models/approved-builds?service_type=ollama" | jq .
```

Use the **`alias`** when registering or calling Inferoute (for example `gguf/qwen3:0.6b`). Pull the model locally with Ollama, then verify the digest from `GET /api/tags` matches `expected_digest`.

### vLLM only

```bash
curl -s "http://localhost/api/models/approved-builds?service_type=vllm" | jq .
```

For each build:

1. Download **`hf_repo`** at the pinned **`hf_revision`** (commit SHA — not floating `main`).
2. Serve with **`alias`** as the model id (for example `--served-model-name Qwen/Qwen3-0.6B`).
3. Point **`model_path`** in the Provider Client config at that directory.

For example:

```bash
hf download Qwen/Qwen3-0.6B --revision <hf_revision from API> --local-dir ~/models/Qwen3-0.6B
vllm serve ~/models/Qwen3-0.6B --served-model-name Qwen/Qwen3-0.6B
```

## Ollama vs vLLM — different builds

The same model family on Ollama and HuggingFace are **separate** approved builds: different `alias`, size, and hash. Consumers pick the backend by model name (`gguf/...` → Ollama providers; bare HF id → vLLM).

| | Ollama | vLLM |
|---|--------|------|
| Example alias | `gguf/qwen3:0.6b` | `Qwen/Qwen3-0.6B` |
| Pin field | `expected_digest` | `weight_fingerprint` + `manifest` |
| Download | `ollama pull qwen3:0.6b` | `hf download` at `hf_revision` |

## Provider Client (automatic)

When verification is enabled, the Provider Client fetches this list on startup and compares your local models against it. You do not need to call the API manually for day-to-day operation — use it to **look up** approved aliases, revisions, and download instructions.


## Related

- [Setup: Ollama](setup-ollama.md)
- [Setup: vLLM](setup-vllm.md)
- [How it works](how-it-works.md)
- [FAQ](faq.md)
