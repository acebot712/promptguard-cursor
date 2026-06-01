[![CI](https://github.com/acebot712/promptguard-plugin/actions/workflows/ci.yml/badge.svg)](https://github.com/acebot712/promptguard-plugin/actions/workflows/ci.yml)
[![License](https://img.shields.io/github/license/acebot712/promptguard-plugin)](https://github.com/acebot712/promptguard-plugin/blob/main/LICENSE)

# PromptGuard Agent Skills

LLM security for AI coding agents — protect applications from prompt injection, PII leakage, and data exfiltration. Works with **Cursor, Claude Code, Codex, Copilot, Gemini CLI, Windsurf, VSCode**, and any MCP-compatible agent.

## Installing the MCP Server

PromptGuard ships a native MCP server built into the CLI. Install the CLI first, then add the MCP server to your agent.

### Prerequisites

- **PromptGuard CLI**:
  - macOS: `brew install promptguard/tap/promptguard`
  - Linux/macOS: `curl -fsSL https://raw.githubusercontent.com/acebot712/promptguard-cli/main/install.sh | sh`
  - Cargo: `cargo install promptguard`
- **PromptGuard account** — sign up at [promptguard.co](https://promptguard.co)
- **API key** — get one from [app.promptguard.co/settings/api-keys](https://app.promptguard.co/settings/api-keys)

### Per-agent setup

<details>
<summary>Install in <b>Cursor</b></summary>
<br />

**One-click install** (opens Cursor and installs the plugin + MCP server):

[Install PromptGuard in Cursor](cursor://anysphere.cursor-deeplink/mcp/install?name=promptguard&config=eyJjb21tYW5kIjoicHJvbXB0Z3VhcmQiLCJhcmdzIjpbIm1jcCIsIi10Iiwic3RkaW8iXX0=)

Or add to `.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "promptguard": {
      "command": "promptguard",
      "args": ["mcp", "-t", "stdio"]
    }
  }
}
```

Cursor also supports the plugin's rules, commands, and agent — install via Settings > Plugins > search "promptguard".

</details>

<details>
<summary>Install in <b>Claude Code</b></summary>
<br />

**One-line MCP install** (fastest — registers the MCP server via the Claude CLI):

```bash
claude mcp add promptguard -- promptguard mcp -t stdio
```

**As a plugin** (includes skills, agents, and the MCP server):

```bash
claude --plugin-dir /path/to/promptguard-plugin
```

To submit to the official Claude Code marketplace: [claude.ai/settings/plugins/submit](https://claude.ai/settings/plugins/submit)

</details>

<details>
<summary>Install in <b>VSCode / Copilot</b></summary>
<br />

Add to your `.vscode/mcp.json`:

```json
{
  "servers": {
    "promptguard": {
      "command": "promptguard",
      "args": ["mcp", "-t", "stdio"]
    }
  }
}
```

</details>

<details>
<summary>Install in <b>Windsurf</b></summary>
<br />

Add to `~/.codeium/windsurf/mcp_config.json`:

```json
{
  "mcpServers": {
    "promptguard": {
      "command": "promptguard",
      "args": ["mcp", "-t", "stdio"]
    }
  }
}
```

</details>

<details>
<summary>Install in <b>Codex</b></summary>
<br />

**As a plugin** (includes skills and MCP server):

Copy to your repo plugins directory and register in your marketplace:

```bash
cp -R /path/to/promptguard-plugin ./plugins/promptguard
```

Add to `.agents/plugins/marketplace.json`:

```json
{
  "name": "local-plugins",
  "plugins": [{
    "name": "promptguard",
    "source": { "source": "local", "path": "./plugins/promptguard" },
    "policy": { "installation": "AVAILABLE", "authentication": "ON_INSTALL" },
    "category": "Security"
  }]
}
```

Or add the MCP server standalone in `.codex/mcp.json`:

```json
{
  "mcpServers": {
    "promptguard": {
      "command": "promptguard",
      "args": ["mcp", "-t", "stdio"]
    }
  }
}
```

</details>

<details>
<summary>Install in <b>Gemini CLI</b></summary>
<br />

Add to `~/.gemini/settings.json`:

```json
{
  "mcpServers": {
    "promptguard": {
      "command": "promptguard",
      "args": ["mcp", "-t", "stdio"]
    }
  }
}
```

</details>

<details>
<summary>Install in <b>any other MCP client</b></summary>
<br />

Run the MCP server via stdio:

```bash
promptguard mcp -t stdio
```

Configure your MCP client to spawn `promptguard mcp -t stdio` as a subprocess.

</details>

## MCP Tools

When connected, the agent can call these tools directly:

| Tool | Description |
|------|-------------|
| `promptguard_auth` | Authenticate with PromptGuard. Opens the dashboard to copy an API key. |
| `promptguard_logout` | Remove locally stored credentials. |
| `promptguard_scan_text` | Scan text for prompt injection, jailbreaks, PII leakage, and toxicity. |
| `promptguard_scan_project` | Scan a project directory for unprotected LLM SDK usage. |
| `promptguard_redact` | Redact PII (emails, phones, SSNs, credit cards, API keys) from text. |
| `promptguard_status` | Show current configuration, active providers, and key type. |

## What's included

Beyond the MCP server, this repo includes agent skills and workflows:

| Component | Description |
|-----------|-------------|
| **Skills** | **Secure LLM Integration** — walks through adding PromptGuard to any project. **PromptGuard API** — teaches the agent exact request/response schemas for scan, redact, and guard endpoints. |
| **Commands** | `/promptguard-scan` finds unprotected LLM calls and hardcoded secrets. `/promptguard-secure` adds PromptGuard to a project end-to-end. |
| **Agent** | LLM Security Reviewer — specialized code reviewer for prompt injection, PII leakage, agent tool abuse, and other LLM-specific vulnerabilities. |
| **Rules** | Always-on guidance ensuring the agent writes secure LLM code when using OpenAI, Anthropic, or any supported provider. |

These components work natively in Cursor (via the plugin system). For other agents, the skills and agent definitions use standard `SKILL.md` format compatible with Claude Code, Codex, and any agent that reads markdown skill files.

## Project attribution

To associate MCP requests with a specific PromptGuard project (for per-project billing and analytics), set the `project_id` in your local `.promptguard.json`:

```json
{
  "version": "1.0",
  "api_key": "pg_live_xxx",
  "project_id": "your-project-id",
  "proxy_url": "https://api.promptguard.co/api/v1"
}
```

Or select a project globally:

```bash
promptguard projects list
promptguard projects select <project-id>
```

## Supported LLM providers

OpenAI, Anthropic, Google Generative AI (Gemini), Cohere, AWS Bedrock, LangChain, CrewAI, LlamaIndex, Vercel AI SDK.

## Links

- [Documentation](https://docs.promptguard.co)
- [Dashboard](https://app.promptguard.co)
- [CLI](https://github.com/acebot712/promptguard-cli)
- [Python SDK](https://github.com/acebot712/promptguard-python)
- [Node.js SDK](https://github.com/acebot712/promptguard-node)
- [Website](https://promptguard.co)
