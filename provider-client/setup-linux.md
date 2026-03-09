# Setup: Linux (NVIDIA)

## Manual install from source

1. **Go:** Install Go 1.21 or higher.

2. **Clone and build:**

   ```bash
   git clone https://github.com/sentnl/inferoute-client.git
   cd inferoute-client
   cp config.yaml.example config.yaml
   nano config.yaml   # set provider API key and other options
   go build -o inferoute-client ./cmd
   ```

3. **Exposure:** The client uses **Cloudflare Tunnel** to expose your machine. No need to install or configure ngrok or open firewall ports. Get your provider API key from the [Inferoute platform](https://core.inferoute.com); the client will request a tunnel from the platform and run cloudflared for you.

4. **Run:**

   ```bash
   ./inferoute-client --config config.yaml
   ```

## NVIDIA GPU

- Install the [NVIDIA driver](https://www.nvidia.com/drivers) so `nvidia-smi` is on `PATH`.
- The client uses `nvidia-smi` for GPU monitoring and busy-state detection. The install script does not install the driver.
