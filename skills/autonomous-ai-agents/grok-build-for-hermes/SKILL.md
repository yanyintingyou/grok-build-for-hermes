---
name: grok-build-for-hermes
description: Use when the user wants Hermes to call or orchestrate the official Grok Build CLI (xAI) for coding tasks, especially in headless mode or via ACP.
version: 1.1.0
author: yanyintingyou
license: MIT
platforms: [linux, macos, windows]
metadata: {"hermes":{"category":"autonomous-ai-agents","tags":["grok","xai","coding-agent","headless","acp"],"related_skills":["codex","hermes-agent"]},"openclaw":{"homepage":"https://github.com/yanyintingyou/grok-build-for-hermes"},"compatibility":{"agents":["Hermes Agent","Claude Code","OpenAI Codex","OpenClaw","generic AgentSkills loaders"]}}
---

# Grok Build for Hermes

This skill enables Hermes to invoke the official **Grok Build CLI** (`grok`) from xAI.

**Important**: This skill was refreshed against xAI Grok Build docs and CLI help on 2026-05-29. The npm package `@xai-official/grok` currently reports `0.2.11`; the coding API model remains `grok-build-0.1`. Treat Grok Build as early beta and re-check `grok --help` after upgrades.

## When to Use

Use this skill when you want Hermes to:

- Run Grok Build in headless mode (`grok -p "..."`)
- Execute one-off coding tasks via the Grok Build agent
- Integrate Grok Build into automated workflows
- Use ACP mode (`grok agent stdio`) for structured integration
- Manage sessions, models, or output formats through Hermes terminal calls

**Do not use** this skill if you want Hermes to use Grok models directly via the `xai-oauth` provider (that is a separate, recommended path).

## Prerequisites

- Grok Build CLI installed and available on `PATH`.
  - Verify with `grok --version`.
  - Install or update Grok Build using the official xAI documentation rather than copying shell-pipe installer commands into this skill.
- Authenticated (either via browser login or `XAI_API_KEY`)
- Running inside a Git repository is recommended (like most coding agents)

## Current Version Check

As of this refresh:

- npm package: `@xai-official/grok@0.2.11` (`latest` / `alpha`)
- binary: `grok`
- API coding model: `grok-build-0.1`
- model page notes: 256k context window; aliases include `grok-code-fast-1`, `grok-code-fast`, and `grok-code-fast-1-0825`

Verify locally before relying on exact flags:

```bash
grok version
grok --help
grok agent --help
grok agent stdio --help
grok inspect --json
```

## One-Shot Headless Execution

```bash
grok -p "Your prompt here"
```

Common flags verified against `@xai-official/grok@0.2.11 --help` and xAI docs:

| Flag | Purpose |
|------|---------|
| `-p, --single <PROMPT>` | Run a single-turn headless prompt and print output. |
| `--prompt-file <PATH>` | Run a single-turn prompt loaded from a file. Prefer this for long prompts to avoid shell quoting issues. |
| `--prompt-json <JSON>` | Run a single-turn prompt as JSON content blocks. |
| `-m, --model <MODEL>` | Select a model ID. The API coding model is still `grok-build-0.1`. |
| `-r, --resume [SESSION_ID]` | Resume a specific session, or the most recent if omitted. |
| `-c, --continue` | Continue the most recent session for the current working directory. |
| `--cwd <CWD>` | Set the working directory. |
| `--output-format <plain|json|streaming-json>` | Output format for headless mode; default is `plain`. |
| `--always-approve` | Auto-approve all tool executions. Use only for well-scoped tasks. |
| `--permission-mode <MODE>` | Select permission behavior. Current CLI values include `default`, `acceptEdits`, `auto`, `dontAsk`, `bypassPermissions`, and `plan`. |
| `--no-alt-screen` | Run inline instead of using the terminal alternate screen. |
| `--no-auto-update` | Skip background update checks in scripts/CI/ACP. xAI docs also mention `[cli] auto_update = false` in `~/.grok/config.toml`. |
| `--no-plan` | Disable plan mode. |
| `--no-subagents` | Disable subagent spawning. |
| `--best-of-n <N>` | Run the task N ways in parallel and pick the best (headless only). |
| `--check` | Append a self-verification loop to the prompt (headless only). |
| `--worktree [NAME]` | Start the session in a new git worktree. |
| `--agent <NAME>` / `--agents <JSON>` | Select or define subagents. |
| `--allow <RULE>` / `--deny <RULE>` | Permission allow/deny rules. |
| `--tools <TOOLS>` / `--disallowed-tools <TOOLS>` | Allow or remove built-in tools. |
| `--disable-web-search` | Disable web search and web fetch tools. |
| `--reasoning-effort <EFFORT>` / `--effort <LEVEL>` | Reasoning/effort controls when supported. |

Example with Hermes `terminal` tool:

```python
terminal(
    command='grok --no-auto-update -p "Refactor the authentication module" --output-format plain',
    workdir="~/projects/my-repo",
    pty=true
)
```

## Background / Long-Running Tasks

For longer tasks, combine with Hermes background mode:

```python
terminal(
    command='grok --no-auto-update -p "Implement the new feature described in the ticket" --always-approve --output-format streaming-json',
    workdir="~/projects/my-repo",
    background=true,
    pty=true,
    notify_on_complete=true
)
```

## ACP Mode (Agent Client Protocol)

Grok Build supports ACP:

```bash
grok agent stdio
```

This allows structured JSON-RPC communication. Hermes can spawn this if needed for advanced integrations.

## Configuration Notes

- Global config lives at `~/.grok/config.toml`.
- xAI's headless/ACP docs recommend `--no-auto-update` in scripts and CI; persistent equivalent:
  ```toml
  [cli]
  auto_update = false
  ```
- Permission behavior can be controlled per invocation with `--permission-mode <MODE>` and `--always-approve`. The current CLI exposes modes such as `default`, `acceptEdits`, `auto`, `dontAsk`, `bypassPermissions`, and `plan`.
- Use `grok inspect` to see discovered config sources, instructions, skills, plugins, hooks, and MCP servers in the current directory. Use `grok inspect --json` when Hermes needs machine-readable output.
- Useful current management commands include `grok models`, `grok sessions`, `grok plugin`, `grok mcp`, `grok memory`, `grok worktree`, and `grok version`.

## Rules

1. Always include `--no-auto-update` when running in scripts, CI, or ACP environments.
2. Prefer `--output-format json` or `streaming-json` when Hermes needs to parse results programmatically.
3. For long prompts, prefer `--prompt-file <PATH>` to avoid shell quoting and token escaping mistakes.
4. For safety, use `--always-approve` or permissive `--permission-mode` values only when the task is well-scoped and low-risk.
5. Grok Build requires authentication. Interactive use opens browser login; headless/server use can rely on `XAI_API_KEY` or cached login (`grok login`).
6. Re-check `grok --help` and `grok version` after upgrades; this skill targets `@xai-official/grok` 0.2.x behavior observed at 0.2.11.

## Limitations (Strictly Based on Docs)

- Headless mode is primarily triggered with `-p, --single`, but the 0.2.x CLI also supports `--prompt-file` and `--prompt-json`.
- ACP is available via `grok agent stdio`. xAI docs describe JSON-RPC methods such as `initialize`, `authenticate`, `session/new`, `session/prompt`, and streaming `session/update` chunks.
- Worktree, subagent, plugin, MCP, memory, and session-management commands exist in the 0.2.x CLI, but this skill only gives orchestration guidance rather than exhaustive CLI documentation.
- Enterprise deployment details are intentionally out of scope; consult current xAI docs before automating deployment.

## Recommended Pattern in Hermes

When the user wants to delegate work to Grok Build:

1. Hermes generates a clear, self-contained prompt.
2. Hermes calls the `terminal` tool with the appropriate `grok -p ...` command.
3. For very long tasks, use `background=true` + `notify_on_complete=true`.

This approach keeps Hermes as the orchestrator while leveraging Grok Build's agent capabilities.