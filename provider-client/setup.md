# Setup wizard

The install script places the binary, then runs **`inferoute-client setup`**. That wizard is how you pick an engine, install it if it is missing, choose an [approved model](approved-models.md) that fits this machine, and write `~/.config/inferoute/config.yaml`.

You do **not** install Ollama, vLLM, vLLM Metal, or FreeToken by hand. The wizard does that, then can start the engine and wait for the first model download.

Re-run anytime you want to change engine, model, or API key:

```bash
inferoute-client setup
```

Re-running updates those fields and leaves server and logging settings in place.

If you have not installed the client yet, start with [Installation](../getting-started/installation.md).

## What the wizard asks

1. **Provider API key** — from **Clusters** → **Settings**. Required. See [Sign up and create a cluster](../getting-started/signup.md).
2. **Inference engine** — only engines that work on this OS are selectable.
3. **Install if missing** — if the engine is not on the machine, the wizard offers to install it.
4. **Model** — fetches the approved catalog, scores each build against this GPU or unified memory, and lists rows that should fit (`runs_well`, `fits`, `tight`). Pick a number from the table.
5. **Start now** — can pull (Ollama) or serve the model, then wait until the engine is healthy.

It then writes config (`engine`, `provider_type`, `llm_url`, `model`, `auto_start`, `engine_bin`) and prints how to start the client.

## Engines by platform

| OS | Wizard options | Default URL |
| --- | --- | --- |
| **Linux** | Ollama or vLLM (vLLM needs `nvidia-smi`) | `http://127.0.0.1:11434` / `http://127.0.0.1:8000` |
| **macOS** (Apple Silicon) | Ollama or vLLM Metal | `http://127.0.0.1:11434` / `http://127.0.0.1:8000` |
| **macOS** (Intel) | Ollama | `http://127.0.0.1:11434` |
| **Windows** (64-bit) | Ollama or FreeToken (FreeToken needs `nvidia-smi`) | `http://127.0.0.1:11434` / `http://127.0.0.1:1919` |

vLLM Metal and FreeToken still set **provider_type** to `vllm` (same OpenAI-compatible API). Change engine with the wizard, not by editing YAML.

First-run model download and load can take a long time. Setup waits up to **2 hours**. If the engine is still fetching weights, check `engine.log` under the [log directory](configuration.md#log-files) and re-run setup or start the client after it is up.

## After the wizard

Start the Inferoute client (not just the LLM engine):

```bash
inferoute-client
```

On Windows, use **Start Menu → Inferoute → Inferoute Client**. See [Setup: Windows](setup-windows.md).

When `auto_start` is on, later client starts will bring the engine up if `llm_url` is down — they will not spawn a second copy if that port is already in use.

Then set prices on **Clusters** → **Models**. See [Model pricing](../provider/model-pricing.md).

## Non-interactive setup

For scripts, pass flags instead of answering prompts. `--yes` does **not** install a missing engine unless you also pass `--install`.

```bash
inferoute-client setup --yes --engine ollama --model gguf/qwen3:0.6b --api-key "your-provider-api-key"
```

| Flag | Purpose |
| --- | --- |
| `--engine` | `ollama`, `vllm`, `vllm-metal`, or `freetoken` |
| `--model` | Catalog alias (must exist for that engine) |
| `--api-key` | Provider API key (or `PROVIDER_API_KEY`) |
| `--yes` | No prompts; requires `--engine`, `--model`, and `--api-key` unless config already has them |
| `--install` | Install the engine if it is missing |
| `--no-start` | Write config only; do not start the engine |
| `--url` | Inferoute API base (catalog + `provider.url`). Wins over `INFEROUTE_URL` |
| `--offline-catalog` | Local approved-builds JSON instead of the network catalog |
| `--config` | Config path (default `~/.config/inferoute/config.yaml`) |

Install scripts that skip the wizard entirely use `INFEROUTE_SKIP_SETUP=1` — see [Installation](../getting-started/installation.md#skip-the-wizard-scripts--ci).

## Related

- [Installation](../getting-started/installation.md)
- [Configuration](configuration.md)
- [Model compatibility check](compatibility.md)
- [Setup: Linux](setup-linux.md)
- [Setup: macOS](setup-mac.md)
- [Setup: Windows](setup-windows.md)
