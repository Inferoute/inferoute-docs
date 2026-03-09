# Setup: Ollama

## Listening on all interfaces (for Docker / remote access)

The client (or another container on the same host) must be able to reach Ollama. If Ollama runs on the host and the client runs in Docker, Ollama should listen on `0.0.0.0` so the container can connect (e.g. via `host.docker.internal`).

### Linux (systemd)

Create an override for the Ollama service:

```bash
sudo mkdir -p /etc/systemd/system/ollama.service.d
sudo nano /etc/systemd/system/ollama.service.d/override.conf
```

Add:

```ini
[Service]
Environment="OLLAMA_HOST=0.0.0.0:11434"
```

Then reload and restart:

```bash
sudo systemctl daemon-reload
sudo systemctl restart ollama
```

### macOS (application)

Set the environment variable, then restart Ollama:

```bash
export OLLAMA_HOST=0.0.0.0:11434
```

For a permanent setting, add that line to `~/.zshrc` or `~/.bashrc`, or use:

```bash
launchctl setenv OLLAMA_HOST 0.0.0.0:11434
```

and restart Ollama and your terminal.

### macOS (Homebrew)

Edit the plist:

```bash
nano /opt/homebrew/opt/ollama/homebrew.mxcl.ollama.plist
```

Add (in plist format):

```xml
<key>EnvironmentVariables</key>
<dict>
  <key>OLLAMA_HOST</key>
  <string>0.0.0.0</string>
</dict>
```

Then:

```bash
brew services restart ollama
```

### Windows

```powershell
set OLLAMA_HOST=0.0.0.0
ollama serve
```

Allow Ollama through the firewall if prompted.

## Using Ollama models via Inferoute

When calling the Inferoute API (orchestrator), use the **gguf/** prefix for Ollama models so the platform routes to Ollama providers:

```json
{
  "model": "gguf/llama2",
  "messages": [
    { "role": "user", "content": "Hello!" }
  ]
}
```

Examples: `gguf/llama2`, `gguf/mistral`, `gguf/codellama`, `gguf/neural-chat`. The client strips the prefix when talking to the local Ollama server.
