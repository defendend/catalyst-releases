# Catalyst Releases

Pre-built binaries for [Catalyst](https://github.com/defendend/catalyst-releases) — AI-powered coding agent CLI with multi-provider LLM support.

## Installation

### Homebrew (macOS / Linux)

```bash
brew tap defendend/catalyst
brew install catalyst
```

### Manual Download

Download the archive for your platform from the [latest release](https://github.com/defendend/catalyst-releases-releases/releases/latest), extract, and place the `catalyst` binary in your `$PATH`.

| Platform | Archive |
|----------|---------|
| macOS (Apple Silicon) | `catalyst-vX.Y.Z-darwin-arm64.tar.gz` |
| macOS (Intel) | `catalyst-vX.Y.Z-darwin-x86_64.tar.gz` |
| Linux (x86_64) | `catalyst-vX.Y.Z-linux-x86_64.tar.gz` |
| Windows (x86_64) | `catalyst-vX.Y.Z-windows-x86_64.zip` |

## Quick Start

```bash
# Set API key for any supported provider
export GLM_API_KEY="your-key"        # Zhipu AI (free tier)
export KIMI_API_KEY="your-key"       # Moonshot
export DEEPSEEK_API_KEY="your-key"   # DeepSeek

# Start interactive chat
catalyst

# One-shot query
catalyst ask "Explain Rust ownership"
```

## Supported Providers

GLM (Zhipu AI), Kimi (Moonshot), DeepSeek, Ollama (local models), OpenAI-compatible APIs.

## More Information

See the [main repository](https://github.com/defendend/catalyst-releases) for full documentation.
