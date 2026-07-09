---
name: grok-build-for-agent
description: Use when any compatible agent (Hermes, Claude Code, OpenAI Codex, OpenClaw, generic AgentSkills loaders, ACP-compatible agents, etc.) wants to call or orchestrate the official Grok Build CLI (xAI) for coding tasks, especially in headless mode or via ACP.
version: 1.3.0
author: yanyintingyou
license: MIT
platforms: [linux, macos, windows]
metadata: {"category":"autonomous-ai-agents","tags":["grok","xai","coding-agent","headless","acp","multi-agent","grok-4.5","mcp","plugin"],"compatibility":{"agents":["Hermes Agent","Claude Code","OpenAI Codex","OpenClaw","generic AgentSkills loaders","ACP-compatible agents"]},"openclaw":{"homepage":"https://github.com/yanyintingyou/grok-build-for-agent"}}
---

# Grok Build for Agent

This skill enables **any compatible agent** to invoke the official **Grok Build CLI** (`grok`) from xAI for coding tasks.

**Important**: This skill was comprehensively refreshed against the latest xAI Grok Build documentation and CLI help on **2026-07-08**. Target version: **v0.2.92** (powered by **Grok 4.5**). The dedicated coding API model `grok-build-0.1` remains available for agentic workflows. Grok Build evolves rapidly — always run `grok version` and `grok --help` (and subcommand --help) locally before relying on exact behavior.

## When to Use

Use this skill when you want a compatible agent to:
- Run Grok Build in headless mode (`grok -p "..."` or `--prompt-file`)
- Execute complex one-off or long-running coding tasks via the Grok Build agent
- Integrate Grok Build into automated workflows, CI, or multi-agent orchestration
- Use ACP mode (`grok agent stdio`) for structured JSON-RPC integration with other systems or agents
- Manage sessions, worktrees, MCP servers, plugins, memory, export/import, and configuration via agent-driven terminal calls
- Leverage Grok 4.5's strong coding and agentic capabilities (with optional grok-build-0.1 for specific API coding tasks)

**Do not use** this skill if you want the agent to use Grok models directly via the `xai-oauth` provider or raw API (that is a separate, recommended path for simple chat/completion use cases).

## Prerequisites

- Grok Build CLI installed and available on `PATH`.
  - Official install: `curl -fsSL https://x.ai/cli/install.sh | bash` (Linux/macOS) or `irm https://x.ai/cli/install.ps1 | iex` (Windows).
  - Verify with `grok --version`.
- Authenticated (first launch opens browser login; headless/server use `export XAI_API_KEY=...` or cached login via `grok login`).
- Running inside a Git repository is strongly recommended.
- For advanced automation, understand permission modes, tool allow/deny lists, and sandbox profiles.

## Current Version Check (as of 2026-07-08)

- CLI version: `grok 0.2.92`
- Primary model: **Grok 4.5** (powers the Grok Build TUI and agentic loops)
- API coding model: `grok-build-0.1` (available via xAI API for agentic coding)
- Key new capabilities in 0.2.9x: improved headless reliability, `/minimal` & `/fullscreen` TUI commands, `ask_user_question` tool toggle, better shell output display, MCP/plugin enhancements, permission mode improvements.

Always verify locally:
```bash
grok version
grok --help
grok agent --help
grok agent stdio --help
grok inspect --json
grok models
```

## One-Shot Headless Execution

```bash
grok -p "Your prompt here"
```

**Recommended flags for automation** (verified against v0.2.92 docs and CLI reference):

| Flag | Purpose |
|------|---------|
| `-p, --prompt <PROMPT>` / `--prompt-file <PATH>` / `--prompt-json <JSON>` | Run single-turn headless prompt (prefer --prompt-file for long/complex prompts). |
| `-m, --model <MODEL>` | Select model (e.g. grok-4.5 or custom via config.toml). |
| `--effort <LEVEL>` / `--reasoning-effort <low|medium|high|xhigh|max>` | Control reasoning effort (interchangeable flags). |
| `--json-schema <SCHEMA>` | Constrain/validate output as JSON schema (excellent for structured parsing by agents). |
| `--output-format <plain|json|streaming-json>` | Control output format for programmatic consumption. |
| `--always-approve` / `--yolo` | Auto-approve all tool calls (use only for well-scoped, low-risk tasks). |
| `--permission-mode <MODE>` | Control permission behavior (default, acceptEdits, auto, dontAsk, bypassPermissions, plan, classifier). |
| `--allow <RULE>` / `--deny <RULE>` | Fine-grained permission rules. |
| `--tools <LIST>` / `--disallowed-tools <LIST>` | Explicitly allow or remove built-in tools. Claude Code aliases supported. |
| `--max-turns <N>` | Hard limit on agent turns (prevents runaway loops). |
| `--no-plan` | Disable Plan Mode. |
| `--no-subagents` | Disable subagent spawning. |
| `--no-memory` / `--experimental-memory` | Disable or enable cross-session memory. |
| `--disable-web-search` | Disable web search and web_fetch tools. |
| `--sandbox <PROFILE>` | Apply sandbox profile for FS/network access. |
| `--rules <TEXT>` / `--system-prompt-override <TEXT>` | Append rules or fully override system prompt. |
| `--cwd <PATH>` | Set working directory. |
| `-r, --resume [<ID>]` / `-c, --continue` | Resume or continue sessions. |
| `--fork-session` | Fork into new session ID when resuming. |
| `--session-id <UUID>` | Use specific session UUID for determinism. |
| `--worktree [<NAME>]` / `--ref <REF>` | Start in new git worktree (optionally from specific ref). |
| `--no-auto-update` | Skip background updates (recommended in scripts/CI/ACP). Persistent: `[cli] auto_update = false` in `~/.grok/config.toml`. |

**Example with agent `terminal` tool (Hermes or similar):**
```python
terminal(
    command='grok --no-auto-update -p "Refactor the authentication module to use JWT with proper error handling" --output-format streaming-json --always-approve --max-turns 15',
    workdir="~/projects/my-repo",
    pty=true
)
```

## Background / Long-Running Tasks

For longer tasks use background mode + notification:
```python
terminal(
    command='grok --no-auto-update -p "Implement the full feature described in the ticket" --always-approve --output-format streaming-json --max-turns 30',
    workdir="~/projects/my-repo",
    background=true,
    pty=true,
    notify_on_complete=true
)
```

## ACP Mode (Agent Client Protocol)

```bash
grok agent stdio
```

This runs Grok Build as a structured JSON-RPC agent over stdin/stdout. Ideal for deep integration with other agent frameworks, IDEs, or custom orchestration layers. Use when you need reliable machine-to-machine communication rather than one-shot prompts.

## Using grok inspect, models, mcp, plugin (New in recent versions)

- `grok inspect [--json]` — Discover rules, skills, plugins, hooks, MCP servers, and config sources in the current directory.
- `grok models` — List available models and their configurations.
- `grok mcp <list|add|remove|doctor>` — Manage MCP servers.
- `grok plugin <list|install|uninstall|update|...>` + `grok plugin marketplace` — Manage plugins and marketplaces.

These are extremely useful for agents to dynamically inspect and extend the environment.

## Session, Worktree, Export & Import Management

- `grok sessions <list|search|delete>`
- `grok worktree <list|show|rm|gc>`
- `grok export <session-id> [output]` — Export transcript as Markdown.
- `grok import [targets...]` — Import sessions from Claude Code (great for cross-agent continuity).

Agents can use these to maintain state, branch work, or migrate context between tools.

## Structured Output & Validation

Prefer `--output-format json` or `streaming-json`, or `--json-schema` for strict schema validation. This makes it much easier for the calling agent to reliably parse results.

## Permission Modes & Safety for Agent-Driven Automation

Grok Build 0.2.x has rich permission controls. For automation:
- Use `--always-approve` (or `--yolo`) only for narrow, well-understood tasks.
- Prefer explicit `--allow` / `--deny` rules and `--tools` / `--disallowed-tools`.
- Consider `--sandbox` profiles for untrusted codebases.
- Monitor with `grok inspect` and limit `--max-turns`.
- Newer modes like `auto` and `classifier` reduce interactive prompts while maintaining safety.

## Configuration Notes

- Global config: `~/.grok/config.toml`
- Use `grok inspect` (or `--json`) to verify discovered configuration, skills, MCPs, etc.
- Persistent auto-update disable: `[cli] auto_update = false`
- Custom models can be defined in config.toml and selected with `-m`.
- Many features (MCP, permissions, memory) can be toggled via env vars, config, or flags.

## Rules

1. **Always** include `--no-auto-update` in scripts, CI, ACP, and automated environments.
2. Prefer structured output (`json`, `streaming-json`, or `--json-schema`) for reliable parsing by the calling agent.
3. For long or complex prompts, use `--prompt-file <PATH>` to avoid quoting/escaping issues.
4. Use `--always-approve` and permissive modes **only** for well-scoped, low-risk tasks. Prefer explicit allow/deny lists.
5. Authentication: headless/server use `XAI_API_KEY`; interactive opens browser.
6. Re-check `grok version`, `grok --help`, and subcommand helps after every upgrade — this skill targets v0.2.92 behavior observed on 2026-07-08.
7. When using MCPs or plugins, verify with `grok mcp` / `grok plugin` and `grok inspect`.
8. Limit `--max-turns` and use tool allowlists to control cost and blast radius in agent-orchestrated runs.

## Limitations (Strictly Based on Latest Docs)

- Many rich TUI features (mouse, fullscreen, Plan Mode visuals, subagent view) are not available or fully usable in pure headless / ACP mode.
- Flag set and behavior can change between minor versions; always verify locally.
- Enterprise/sandbox/advanced permission features may require specific deployment context.
- Full ACP JSON-RPC method documentation is still evolving; test `grok agent stdio` thoroughly for production integrations.
- This skill provides orchestration guidance and curated flag recommendations, not exhaustive CLI reference.

## Recommended Pattern in Compatible Agents

When delegating work to Grok Build:

1. The calling agent generates a clear, self-contained prompt (or prompt file).
2. The agent calls its `terminal` tool (or equivalent) with carefully chosen `grok -p ...` flags, preferring structured output and safety limits.
3. For very long tasks, use background execution + completion notification.
4. For deep integration, spawn `grok agent stdio` and communicate via ACP/JSON-RPC.
5. Use `grok inspect`, `grok mcp`, `grok plugin`, session/worktree commands to manage environment and state.
6. After completion, optionally export transcripts or import context from other tools (e.g. Claude Code).

This keeps the calling agent as the orchestrator while fully leveraging Grok Build's powerful coding agent capabilities (Grok 4.5 + tooling).

The dual-layout design (root SKILL.md + packaged copy) ensures this skill works seamlessly across Hermes, Claude Code, OpenAI Codex, OpenClaw, and generic AgentSkills loaders.