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

## No generated types — but the prose drifts, and that is the real risk

`promptguard-python` and `promptguard-node` generate their API types from the
published OpenAPI spec, on a weekly `sync-from-api.yml` that opens a PR when the
spec moves. **This repo has no such workflow, and never should.** Recorded
2026-08-11.

There is no code here to generate types *for*: this repo is markdown rules,
`SKILL.md` files, agent definitions and an `mcp.json` that launches the CLI. It
makes no HTTP request. Nothing in it compiles, imports, or deserializes.

**The exposure is different in kind, and it is worse.** Instead of types, this
repo restates API facts as prose that an LLM will read as authoritative —
`skills/promptguard-api/references/api-reference.md` carries tables of threat
types, PII entity types and plan rate limits. Nothing checks them, and a
generator cannot: they are prose, and the two that matter most are not in the
published spec to generate from. `openapi-developer.json` types `threatType` as
a bare nullable string, so the enum lives only in the backend's `ThreatType`
StrEnum.

That table was **already wrong when this decision was written** — it listed
`injection`, `jailbreak`, `pii`, `exfiltration`, `api_key`, `fraud`,
`tool_injection`, `url_filter`, `multi_turn`, and the API returns none of those.
The real values are `prompt_injection`, `pii_leak`, `data_exfiltration`,
`api_key_leak`, `fraud_abuse`, `mcp_violation`, `url_violation` and others;
`jailbreak` and `multi_turn` do not exist at all. It has been corrected against
`ThreatType` in `backend/api/shared/security/engine.py`.

**So the rule for this repo is a review rule, not a pipeline:** any statement of
a concrete API value — a threat type, a PII entity name, a plan limit, an
endpoint path — must be checked against the backend at the time it is written,
and the file it came from named next to it. A wrong value here does not fail a
build; it teaches an agent something false about a security product.

**Revisit this if** the developer spec starts publishing real enums for these.
Generating the tables from it would then be strictly better than checking them
by hand.

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

## Agent skills

### Issue tracker

Issues live in this repo's GitHub Issues, worked via the `gh` CLI. See `docs/agents/issue-tracker.md`.

### Triage labels

The five canonical triage roles, each label string equal to its name. See `docs/agents/triage-labels.md`.

### Domain docs

Single-context: `CONTEXT.md` plus `docs/adr/` at the repo root, both created lazily. See `docs/agents/domain.md`.
