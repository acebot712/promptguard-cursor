# AGENTS.md

## Overview

Multi-agent plugin for PromptGuard. Provides rules, skills, slash commands, and an MCP server configuration for AI-assisted LLM security across Cursor, Claude Code, Codex, Copilot, Gemini CLI, Windsurf, VSCode, and any MCP-compatible agent.

This repo contains static configuration files (Markdown, JSON) with no build step or automated tests. Each agent loads its own manifest: `.cursor-plugin/plugin.json`, `.claude-plugin/plugin.json`, `.codex-plugin/plugin.json`.

## Repository Layout

```
.cursor-plugin/        # Cursor manifest
.claude-plugin/        # Claude Code manifest (skills + mcpServers wiring)
.codex-plugin/         # Codex manifest (skills + mcpServers + interface)
└── plugin.json        # one plugin.json per directory above

rules/                 # Agent rules (.mdc files)
├── secure-llm-usage.mdc
└── llm-security-review.mdc

skills/                # Agent skills (SKILL.md format)
└── secure-llm-integration/
    ├── SKILL.md
    └── references/

commands/              # Slash commands
├── promptguard-scan.md
└── promptguard-secure.md

agents/                # Agent definitions
└── llm-security-reviewer.md

mcp.json               # MCP server registration (points to promptguard CLI)
```

## How It Works

The plugin connects to the PromptGuard CLI via MCP (`promptguard mcp -t stdio`). Rules and skills guide the agent's AI to follow LLM security best practices.

## Development

No build step. To test changes:

1. Install this repo into the target agent (see the README's per-agent setup)
2. Verify rules/skills appear in the agent
3. Test slash commands work as expected
4. Verify MCP server connects (requires `promptguard` CLI on PATH)

## Coding Standards

- Rules use `.mdc` format (Cursor rule syntax; other agents read the same files)
- Skills follow `SKILL.md` format with clear trigger conditions
- JSON files must be valid (validate before committing)
- Markdown should be clear and actionable for AI agents

## Boundaries

### Never
- Commit API keys, tokens, or credentials
- Reference internal/private repository paths or infrastructure details
- Add binary files or large assets
