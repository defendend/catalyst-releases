# Catalyst

AI-powered coding agent CLI with multi-provider LLM support. Works like Claude Code but with your choice of provider.

## Features

- **Agent mode** — reads, writes, and edits files, runs shell commands, searches code
- **Multi-provider** — GLM (Zhipu), Kimi (Moonshot), DeepSeek, Ollama (local), OpenAI-compatible
- **Interactive TUI** — streaming responses, syntax highlighting, keyboard navigation
- **10 built-in tools** — ReadFile, WriteFile, Edit, Shell, Glob, Grep, WebFetch, AstIndex, Memory, TodoWrite
- **MCP support** — connect external Model Context Protocol servers
- **Architect mode** — dual-model flow: one plans, another executes
- **Git checkpoints** — auto-commits after agent changes, `/undo` to revert
- **Hooks system** — user-defined pre/post tool execution hooks
- **Watch mode** — monitors files for `// AI:` comments and processes them
- **Session persistence** — save/load conversations, auto-save on each response
- **Context compaction** — automatic history summarization when context fills up
- **Permission system** — granular control over which tools can run automatically

## Installation

### Homebrew (macOS / Linux)

```bash
brew tap defendend/catalyst
brew install catalyst
```

### Manual Download

Download the archive for your platform from the [latest release](https://github.com/defendend/catalyst-releases/releases/latest), extract, and place the `catalyst` binary in your `$PATH`.

| Platform | Archive |
|----------|---------|
| macOS (Apple Silicon) | `catalyst-vX.Y.Z-darwin-arm64.tar.gz` |
| macOS (Intel) | `catalyst-vX.Y.Z-darwin-x86_64.tar.gz` |
| Linux (x86_64) | `catalyst-vX.Y.Z-linux-x86_64.tar.gz` |
| Windows (x86_64) | `catalyst-vX.Y.Z-windows-x86_64.zip` |

## Quick Start

```bash
# Set an API key for any provider
export GLM_API_KEY="your-key"        # Zhipu AI (free tier available)
export KIMI_API_KEY="your-key"       # Moonshot
export DEEPSEEK_API_KEY="your-key"   # DeepSeek

# Start interactive chat
catalyst

# One-shot query
catalyst ask "Explain Rust ownership"

# Use specific provider/model
catalyst -p kimi -M kimi-k2.5
catalyst -p deepseek -M deepseek-chat
catalyst -p ollama -M qwen3:8b
```

## Providers

### GLM (Zhipu AI)

Free tier available. Best for getting started without a credit card.

```bash
export GLM_API_KEY="glm_xxxxxxxxxxxxx"
catalyst -p glm
```

| Model | Description | Context |
|-------|-------------|---------|
| `glm-4.7` | Flagship, highest performance | 200K |
| `glm-4.7-flash` | Free, lightweight | 200K |
| `glm-4.7-flashx` | Fast, affordable | 200K |
| `glm-4.6v-flash` | Vision, free | 200K |
| `glm-4.5-flash` | Previous gen, free | 128K |

### Kimi (Moonshot)

Best reasoning capabilities with kimi-k2 series.

```bash
export KIMI_API_KEY="sk-xxxxxxxxxxxxx"
catalyst -p kimi
```

| Model | Description | Context |
|-------|-------------|---------|
| `kimi-k2.5` | Latest multimodal + reasoning | 262K |
| `kimi-k2-thinking` | Deep reasoning (like o1) | 262K |
| `kimi-k2-turbo-preview` | Fast K2 | 262K |
| `kimi-latest` | Latest stable, vision | 131K |
| `moonshot-v1-128k` | Long context | 131K |
| `moonshot-v1-32k` | Balanced | 32K |
| `moonshot-v1-8k` | Fast, cheap | 8K |

### DeepSeek

Strong reasoning with deepseek-reasoner (R1).

```bash
export DEEPSEEK_API_KEY="sk-xxxxxxxxxxxxx"
catalyst -p deepseek
```

| Model | Description | Context |
|-------|-------------|---------|
| `deepseek-chat` | DeepSeek V3.2, fast flagship | 128K |
| `deepseek-reasoner` | DeepSeek R1, thinking mode | 128K |

### Ollama (Local)

Run models locally with [Ollama](https://ollama.ai). No API key needed.

```bash
# Start Ollama server first
ollama serve

# Use with default localhost
catalyst -p ollama -M qwen3:8b

# Custom Ollama host
export OLLAMA_HOST="http://192.168.1.100:11434"
catalyst -p ollama
```

Any model pulled into Ollama works — just pass its name with `-M`. Default models:

| Model | Description | Context |
|-------|-------------|---------|
| `qwen3` | Qwen 3 (default) | 128K |
| `llama3.3` | Meta Llama 3.3 | 128K |
| `deepseek-r1` | DeepSeek R1 reasoning | 128K |
| `gemma3` | Google Gemma 3 | 128K |
| `mistral` | Mistral 7B | 32K |

### OpenAI-Compatible

Works with OpenAI API or any compatible endpoint (Azure OpenAI, Together AI, Groq, LM Studio, etc.).

```bash
export OPENAI_API_KEY="sk-xxxxxxxxxxxxx"
catalyst -p openai

# Custom endpoint (Azure, Together AI, LM Studio, etc.)
export OPENAI_BASE_URL="https://your-endpoint.com/v1"
export OPENAI_API_KEY="your-key"
catalyst -p openai -M your-model-name
```

Default models (accepts any model name):

| Model | Description | Context |
|-------|-------------|---------|
| `gpt-4o` | OpenAI GPT-4o, multimodal | 128K |
| `gpt-4o-mini` | Fast and affordable | 128K |
| `o3-mini` | Reasoning model | 200K |

### Saving Provider Configuration

```bash
# Set API key for a provider (persists to config file)
catalyst config --provider glm --api-key "glm_xxxxxxxxxxxxx"
catalyst config --provider kimi --api-key "sk-xxxxxxxxxxxxx"
catalyst config --provider deepseek --api-key "sk-xxxxxxxxxxxxx"

# Ollama doesn't need an API key
catalyst config --provider ollama

# View current configuration
catalyst config
```

Catalyst auto-detects the first configured provider on startup. To switch providers mid-session, use `/provider`.

## CLI Commands

```bash
catalyst                      # Interactive chat (default)
catalyst ask "message"        # One-shot query (cached for 1 hour)
catalyst shell "description"  # Generate and run shell command
catalyst watch [path]         # Watch for AI comments in files
catalyst -p <provider>        # Use specific provider
catalyst -M <model>           # Use specific model
catalyst models               # List available models
catalyst providers            # List available providers
catalyst config               # Show configuration
catalyst config --provider glm --api-key KEY  # Set API key
catalyst config --clear-cache # Clear response cache
```

## Chat Commands

| Command | Description |
|---------|-------------|
| `/help` | Show available commands |
| `/agent` | Toggle agent mode (tools) |
| `/model` | Select model (interactive picker) |
| `/provider` | Select provider (interactive picker) |
| `/architect [model]` | Toggle architect mode (dual-model) |
| `/save [name]` | Save session |
| `/load <id>` | Load session |
| `/sessions` | List saved sessions |
| `/usage` | Show token usage and cost |
| `/compact` | Compact conversation history |
| `/undo` | Revert last git checkpoint |
| `/checkpoints` | List git checkpoints |
| `/clear` | Clear conversation history |
| `/copy` | Copy chat to clipboard |
| `/exit` | Exit |

## Key Bindings

| Key | Action |
|-----|--------|
| Enter | Send message |
| Shift+Enter | New line |
| Up/Down | Input history |
| Ctrl+C | Cancel streaming / Exit |
| Ctrl+T | Toggle reasoning display |
| Ctrl+E | Expand/collapse last tool output |
| PageUp/Down | Scroll history |
| Tab | Autocomplete commands |

## Agent Mode

Toggle with `/agent`. The model gets access to 10 tools for autonomous coding tasks.

### Tools

| Tool | Description |
|------|-------------|
| **ReadFile** | Read file contents with line numbers |
| **WriteFile** | Create or overwrite files |
| **Edit** | Find and replace with diff output |
| **Shell** | Execute shell commands (with security checks) |
| **Glob** | Find files by pattern |
| **Grep** | Search code with regex |
| **WebFetch** | Fetch and parse web pages |
| **AstIndex** | AST-based semantic code search |
| **Memory** | Save/recall facts across sessions |
| **TodoWrite** | Track multi-step task progress |

### Permission System

When a tool requires approval, you'll be prompted with four options:

- **Allow Once** — allow this specific call only
- **Allow Session** — allow all calls to this tool for the current session
- **Allow Always** — persist to `.catalyst/settings.local.json` (permanent)
- **Deny** — skip this tool call

Read-only tools (`ReadFile`, `Glob`, `Grep`, `AstIndex`) are auto-allowed. Configure persistent permissions in `.catalyst/settings.local.json`:

```json
{
  "allow": [
    "Shell:git *",
    "Shell:cargo *",
    "WriteFile"
  ],
  "deny": [
    "Shell:rm -rf *"
  ]
}
```

Pattern matching supports tool name (`"Shell"`) and tool + args prefix (`"Shell:git *"`).

### Git Checkpoints

In agent mode, Catalyst automatically creates git commits after file modifications. Each checkpoint is prefixed with `[catalyst-checkpoint]`.

- `/undo` — revert the last checkpoint (`git reset --soft HEAD~1`)
- `/checkpoints` — list recent checkpoints

Only works inside a git repository.

## Architect Mode

Architect mode uses a dual-model flow for complex tasks: one model plans, another executes.

```
/architect deepseek-reasoner
```

### How it works

1. **Phase 1 — Architect (planning)**: The architect model analyzes the codebase using read-only tools (`ReadFile`, `Glob`, `Grep`, `AstIndex`) and produces a step-by-step implementation plan. Maximum 15 iterations.

2. **Phase 2 — Editor (execution)**: Your current model receives the architect's plan and executes it with full tool access (`WriteFile`, `Edit`, `Shell`, etc.).

### Usage

```
# Enable with a thinking model as architect
/architect deepseek-reasoner
/architect kimi-k2-thinking

# Send a message — it goes through the dual flow automatically
> Refactor the auth module to use JWT tokens

# Disable architect mode
/architect
```

The architect model can be from a different provider than your current one. This lets you use a powerful reasoning model for planning and a faster model for execution.

## Watch Mode

Monitors files for AI comments and processes them automatically.

```bash
catalyst watch              # Watch current directory
catalyst watch src/         # Watch specific path
```

### Supported comment patterns

```rust
// AI: add error handling here
// AI? should this be async?
// TODO(ai): implement validation
```

```python
# AI: refactor this function
# AI? add type hints
# TODO(ai): extract to module
```

```html
<!-- AI: improve accessibility -->
```

```c
/* AI: document this struct */
```

When a file is saved with an AI comment, Catalyst sends the surrounding code context to the LLM and replaces the comment with the generated code.

### Watched file types

`rs`, `py`, `js`, `ts`, `tsx`, `jsx`, `go`, `java`, `kt`, `rb`, `cpp`, `c`, `h`, `cs`, `swift`, `md`, `toml`, `yaml`, `yml`, `json`, `html`, `css`, `scss`, `sh`, `bash`, `zsh`

## Shell Assistant

Generate and execute shell commands from natural language descriptions.

```bash
catalyst shell "find all files larger than 10MB"
catalyst shell "compress all PNGs in current directory"
catalyst shell "show git log for last week"
```

The LLM generates a command, shows it for review, and you can:
- **y** — execute the command
- **e** — edit the command before executing
- **n** — cancel

## Project Configuration

Create a `.catalyst/` directory in your project root for per-project settings.

```
project-root/
└── .catalyst/
    ├── INSTRUCTIONS.md         # System prompt (any provider)
    ├── GLM.md                  # GLM-specific instructions
    ├── KIMI.md                 # Kimi-specific instructions
    ├── DEEPSEEK.md             # DeepSeek-specific instructions
    ├── hooks.json              # Pre/post tool execution hooks
    ├── mcp.json                # MCP server connections
    ├── settings.local.json     # Tool permission rules
    ├── memory.md               # Persistent agent memory
    └── rules/                  # Auto-loaded rule files
        ├── code-style.md
        └── conventions.md
```

### System Prompt Instructions

Provider-specific instruction files are loaded into the system prompt automatically. Catalyst looks for them in this priority order:

1. `{PROVIDER}.md` — e.g., `GLM.md` when using GLM provider
2. `INSTRUCTIONS.md` — fallback for any provider

Example `.catalyst/INSTRUCTIONS.md`:

```markdown
# Project Context

This is a React + TypeScript web application.

## Conventions
- Use functional components with hooks
- Use Tailwind CSS for styling
- All API calls go through src/api/client.ts

## Important
- Never modify files in src/generated/
- Run `npm test` after making changes
```

### Rules Directory

All `.md` files in `.catalyst/rules/` are loaded alphabetically and appended to the system prompt. Use them for coding standards, architecture rules, or project-specific conventions.

```markdown
<!-- .catalyst/rules/code-style.md -->
# Code Style

- Use 2-space indentation
- Prefer const over let
- Always use TypeScript strict mode
```

### Hooks (`hooks.json`)

User-defined shell commands that run before or after tool execution.

```json
{
  "hooks": [
    {
      "event": "PreToolUse",
      "pattern": "Shell",
      "command": "echo \"Running: $CATALYST_TOOL_ARGS\" >> .catalyst/audit.log",
      "timeout_secs": 5
    },
    {
      "event": "PostToolUse",
      "pattern": "WriteFile",
      "command": "cargo fmt -- --check 2>/dev/null || true"
    },
    {
      "event": "PreToolUse",
      "pattern": "Shell",
      "command": "echo $CATALYST_TOOL_ARGS | grep -q 'rm ' && exit 1 || exit 0"
    }
  ]
}
```

**Available events**: `PreToolUse`, `PostToolUse`, `SessionStart`, `SessionEnd`

**Hook fields**:
| Field | Required | Description |
|-------|----------|-------------|
| `event` | Yes | When to trigger |
| `pattern` | No | Tool name to match (e.g., `"Shell"`, `"WriteFile"`). Omit to match all tools |
| `command` | Yes | Shell command to execute |
| `timeout_secs` | No | Timeout in seconds (default: 30) |

**Environment variables** passed to hook commands:
| Variable | Description |
|----------|-------------|
| `CATALYST_TOOL_NAME` | Name of the tool (e.g., `Shell`) |
| `CATALYST_TOOL_ARGS` | JSON string of tool arguments |
| `CATALYST_WORK_DIR` | Working directory |
| `CATALYST_EVENT` | Event type (e.g., `PreToolUse`) |

A `PreToolUse` hook returning exit code 0 allows the tool to proceed; non-zero blocks execution.

### MCP Servers (`mcp.json`)

Connect external [Model Context Protocol](https://modelcontextprotocol.io/) servers to extend the agent with additional tools.

```json
{
  "servers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/path/to/allowed/dir"],
      "env": {}
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "ghp_xxxxxxxxxxxxx"
      }
    }
  }
}
```

**Server config fields**:
| Field | Required | Description |
|-------|----------|-------------|
| `command` | Yes | Executable to run (e.g., `npx`, `node`, `python`) |
| `args` | No | Command-line arguments (default: `[]`) |
| `env` | No | Environment variables (default: `{}`) |

MCP tools are discovered via the `tools/list` protocol method and appear alongside built-in tools in agent mode.

### Persistent Memory (`memory.md`)

The `Memory` tool reads and writes to `.catalyst/memory.md`. The agent uses this to remember project conventions, user preferences, and debugging insights across sessions.

The file is also loaded into the system prompt at the start of each session. Format:

```markdown
## User Preferences
User prefers functional style over OOP

## Project Conventions
Use snake_case for all database columns
Always run migrations with --dry-run first

## Debugging Notes
The auth service requires Redis to be running on port 6380 (non-default)
```

### Tool Permissions (`settings.local.json`)

Persistent permission rules for agent tools. See [Permission System](#permission-system) above.

## Configuration

### Environment Variables

```bash
# Provider API keys
export GLM_API_KEY="glm_xxxxxxxxxxxxx"
export KIMI_API_KEY="sk-xxxxxxxxxxxxx"
export DEEPSEEK_API_KEY="sk-xxxxxxxxxxxxx"
export OPENAI_API_KEY="sk-xxxxxxxxxxxxx"

# Custom endpoints
export OPENAI_BASE_URL="https://your-endpoint.com/v1"
export OLLAMA_HOST="http://localhost:11434"

# Defaults
export GLM_PROVIDER="glm"          # Default provider
export GLM_MODEL="glm-4.7"         # Default model
```

### Config File Location

- **macOS**: `~/Library/Application Support/com.defendend.catalyst/config.json`
- **Linux**: `~/.config/com.defendend.catalyst/config.json`
- **Windows**: `%APPDATA%\defendend\catalyst\config.json`

## License

MIT
