# OpenClaw Docker

> **Reference**: [OpenClaw Docker Installation](https://docs.openclaw.ai/install/docker)

## Introduction

This repository provides all relevant files and usage guides for the pre-built OpenClaw Docker image. It is specifically configured to modify port settings so that the image listens only on local ports. This prevents access from external networks, significantly enhancing the security of your local deployment.

## Prerequisites

- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/install/)

## Installation

1. **(Optional) Clean previous installations**  
   If you have a previous configuration and want a fresh start, remove the existing `.openclaw` directory:
   ```bash
   rm -rf ~/.openclaw
   ```

2. **Run the setup script**  
   Export the image environment variable and run the installation script:
   ```bash
   export OPENCLAW_IMAGE="ghcr.io/openclaw/openclaw:latest"
   ./scripts/docker/setup.sh
   ```

3. **Secure Port Binding**  
   Ensure your `docker-compose.yml` explicitly binds to `127.0.0.1` for security (this is often handled by default in this repo's `docker-compose.yml`):
   ```yaml
   ports:
     - "127.0.0.1:${OPENCLAW_GATEWAY_PORT:-18789}:18789"
     - "127.0.0.1:${OPENCLAW_BRIDGE_PORT:-18790}:18790"
   ```
   After verifying, run:
   ```bash
   ./docker-setup.sh
   ```

## Usage

### Starting and Stopping

Start the services in the background:
```bash
docker compose up -d
```

Stop the services:
```bash
docker compose down
```

### Updating the Image

To fetch the latest version of the image:
```bash
docker compose pull
docker compose up -d
```

### CLI Commands

When using the CLI, invoke it through the Docker container instead of running the `openclaw` command directly. Run:
```bash
docker compose run --rm openclaw-cli <command>
```

## Advanced Configuration

All configurations reside in the `~/.openclaw/` directory within your host.

### Bypassing Device Pairing
If you have a pending device connection and want to bypass manual pairing, edit `~/.openclaw/devices/pending.json` to enable silent pairing:
```json
{
  "silent": true
}
```

### Gateway Bindings
To set your gateway correctly, update `~/.openclaw/openclaw.json` to bind to the LAN:
```json
{
  "gateway": {
    "port": 18789,
    "mode": "local",
    "bind": "lan"
  }
}
```

### Model Provider: Local Ollama setup
To configure a local Ollama instance as your model provider, configure `~/.openclaw/openclaw.json`:

```json
{
  "models": {
    "providers": {
      "ollama": {
        "baseUrl": "http://host.docker.internal:11434/v1",
        "apiKey": "ollama-local",
        "api": "ollama",
        "models": [
          {
            "id": "gpt-oss:20b",
            "name": "GPT-OSS 20B",
            "reasoning": false,
            "input": ["text"],
            "cost": { "input": 0, "output": 0, "cacheRead": 0, "cacheWrite": 0 },
            "contextWindow": 8192,
            "maxTokens": 81920
          }
        ]
      }
    }
  }
}
```
*(Note: Older configurations might use `"api": "openai-completions"`, but the explicit `"api": "ollama"` setup is recommended.)*

### Default Agent Strategy
To specify Ollama as the default agent and set up fallbacks, edit `~/.openclaw/openclaw.json`:
```json
{
  "agents": {
    "defaults": {
      "model": {
        "primary": "ollama/gpt-oss:20b",
        "fallbacks": [
          "ollama/llama3.3",
          "ollama/qwen2.5-coder:32b"
        ]
      }
    }
  }
}
```
