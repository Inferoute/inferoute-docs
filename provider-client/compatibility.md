# Model compatibility check

Before you install models or start the provider client as a daemon, you can check which **approved Inferoute models** are likely to fit on this machine.

The command detects local hardware and scores each approved build. It does **not** start the provider daemon and does **not** need an API key.

It reads the public approved-model catalog from `https://core.inferoute.com` by default. It does not read your provider configuration file unless you explicitly pass a different URL with `--catalog-url`.

## Requirements

| Platform | What it uses |
|----------|----------------|
| **Linux + NVIDIA** | `nvidia-smi` for GPU name, driver, CUDA, VRAM total/free |
| **Windows + NVIDIA** | `nvidia-smi` (same as Linux) when the NVIDIA driver is on `PATH` |
| **macOS (Apple Silicon)** | `system_profiler` for GPU name; unified memory from system RAM |
| **macOS (Intel) / CPU-only / Windows without NVIDIA** | System RAM with a slow/CPU warning |

## Commands

```bash
inferoute-client compatibility
inferoute-client compatibility --provider-type ollama
inferoute-client compatibility --provider-type vllm
inferoute-client compatibility --json
inferoute-client compatibility --catalog-url https://core.inferoute.com
inferoute-client compatibility --offline-catalog ./approved-models.json
```

| Flag | Purpose |
|------|---------|
| `--provider-type` | `ollama`, `vllm`, or omit for both |
| `--catalog-url` | Inferoute API base (default `https://core.inferoute.com`) |
| `--offline-catalog` | Local JSON in the same shape as the public catalog |
| `--json` | Machine-readable output for scripts |
| `--show-too-large` | Include `too_large` rows (default: on) |

For example, to use another Inferoute API deployment:

```bash
inferoute-client compatibility --catalog-url https://api.example.com
```

## Statuses

| Status | Meaning |
|--------|---------|
| `runs_well` | Comfortable headroom |
| `fits` | Should load with normal overhead |
| `tight` | Little headroom; may struggle under load |
| `too_large` | Unlikely to fit |
| `unknown` | Missing size or usable memory |

Scoring uses each build’s public `min_size_bytes` plus a conservative runtime overhead (higher for vLLM). On Apple Silicon, only a fraction of unified memory is treated as usable so the OS still has headroom. On multi-GPU Linux or Windows hosts, v1 scores against the **largest single GPU**.

This is a fit check only — it does not estimate tokens/sec.

## Troubleshooting

### Every model is `unknown`

If every row shows `0 B` and `model size unavailable in catalog`, the API response does not include a positive `min_size_bytes` value. Update the Inferoute API deployment and confirm the approved builds contain measured sizes.

### Catalog request fails

An HTTP `522` response means the API origin timed out behind Cloudflare. It is not caused by a missing provider client configuration file. Retry after the API is available, pass another deployment with `--catalog-url`, or use `--offline-catalog`.

### Linux or Windows GPU is not detected

Run `nvidia-smi` directly. If it is unavailable, install the NVIDIA driver and ensure the command is on `PATH`. In containers, expose the GPU with the NVIDIA container runtime.

## Related

- [Approved model builds](approved-models.md)
- [Setup: Linux](setup-linux.md)
- [Setup: macOS](setup-mac.md)
- [Setup: Windows](setup-windows.md)
