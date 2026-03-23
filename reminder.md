# Installation Reminder
- (Optional) rm -rf ~/.openclaw
- set port to localhost for security
'''
<!-- docker-compose.yml -->
  ports:
    - "127.0.0.1:${OPENCLAW_GATEWAY_PORT:-18789}:18789"
    - "127.0.0.1:${OPENCLAW_BRIDGE_PORT:-18790}:18790"
'''
- ./docker-setup.sh

- vim ~/.openclaw/devices/pending.json (bypass paring if pending)
'''
"silent": false => true
'''

# Usage Reminder
1. Use "docker compose run --rm openclaw-cli" instead of "openclaw" to run CLI commands

2. Set "gateway" to "lan"
> vim ~/.openclaw/openclaw.json
'''
  "gateway": {
    "port": 18789,
    "mode": "local",
    "bind": "lan",
    ...
    },
'''

3. Set Ollama as model provider
> vim ~/.openclaw/openclaw.json
'''
<!-- Explicit setup (new) -->
{
  models: {
    providers: {
      ollama: {
        baseUrl: "http://host.docker.internal:11434/v1",
        apiKey: "ollama-local",
        api: "ollama",
        models: [
          {
            id: "gpt-oss:20b",
            name: "GPT-OSS 20B",
            reasoning: false,
            input: ["text"],
            cost: { input: 0, output: 0, cacheRead: 0, cacheWrite: 0 },
            contextWindow: 8192,
            maxTokens: 8192 * 10
          }
        ]
      }
    }
  }
}
'''
'''
<!-- Explicit setup (old) -->
{
  models: {
    providers: {
      ollama: {
        baseUrl: "http://host.docker.internal:11434/v1",
        api: "openai-completions",
        apiKey: "ollama-local",
        models: [...]
      }
    }
  }
}
'''

4. Set Ollama as default agent
'''
{
  agents: {
    defaults: {
      model: {
        primary: "ollama/gpt-oss:20b",
        fallbacks: ["ollama/llama3.3", "ollama/qwen2.5-coder:32b"],
      },
    },
  },
}
'''

