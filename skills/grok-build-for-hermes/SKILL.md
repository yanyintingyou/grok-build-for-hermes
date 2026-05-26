---
name: grok-build-for-hermes
description: Use when the user wants Hermes to call or orchestrate the official Grok Build CLI (xAI) for coding tasks, especially in headless mode or via ACP.
version: 1.0.1
author: yanyintingyou
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [grok, xai, coding-agent, headless, acp]
    related_skills: [codex, hermes-agent]
---

# Grok Build for Hermes

This skill enables Hermes to invoke the official **Grok Build CLI** (`grok`) from xAI.

**Important**: This skill is based strictly on the official documentation at https://docs.x.ai/build/ as of May 2026. It only covers features explicitly documented at that time. Grok Build is in early beta.

## When to Use

Use this skill when you want Hermes to:

- Run Grok Build in headless mode (`grok -p "..."`)
- Execute one-off coding tasks via the Grok Build agent
- Integrate Grok Build into automated workflows
- Use ACP mode (`grok agent stdio`) for structured integration
- Manage sessions, models, or output formats through Hermes terminal calls

**Do not use** this skill if you want Hermes to use Grok models directly via the `xai-oauth` provider (that is a separate, recommended path).

## Prerequisites

- Grok Build CLI installed:
  ```bash
  curl -fsSL https://x.ai/cli/install.sh | bash
  ```
- Authenticated (either via browser login or `XAI_API_KEY`)
- Running inside a Git repository is recommended (like most coding agents)

## One-Shot Headless Execution

```bash
grok -p "Your prompt here"
```

Common flags (from official docs):

| Flag                        | Purpose                                              |
|----------------------------|------------------------------------------------------|
| `-p, --single`             | Send a single prompt (headless)                      |
| `-m, --model`              | Select a specific model                              |
| `-s, --session-id`         | Create or resume a named headless session            |
| `-r, --resume`             | Resume an existing session                           |
| `-c, --continue`           | Continue the most recent session in current directory|
| `--cwd`                    | Set the working directory                            |
| `--output-format`          | `plain`, `json`, or `streaming-json`                 |
| `--always-approve`         | Auto-approve tool executions                         |
| `--no-alt-screen`          | Run inline (no fullscreen TUI)                       |
| `--no-auto-update`         | Skip background update checks (recommended in scripts)|

Example with Hermes `terminal` tool:

```python
terminal(
    command='grok -p "Refactor the authentication module" --output-format plain --no-auto-update',
    workdir="~/projects/my-repo",
    pty=true
)
```

## Background / Long-Running Tasks

For longer tasks, combine with Hermes background mode:

```python
terminal(
    command='grok -p "Implement the new feature described in the ticket" --always-approve --no-auto-update',
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

- Global config lives at `~/.grok/config.toml`
- Permission behavior can be set with:
  ```toml
  [ui]
  permission_mode = "always-approve"   # or "ask"
  ```
- Use `grok inspect` to see discovered skills, plugins, MCP servers, etc. in the current directory.

## Rules

1. Always include `--no-auto-update` when running in scripts or automated environments.
2. Prefer `--output-format json` or `streaming-json` when Hermes needs to parse results programmatically.
3. For safety, consider using `--always-approve` only when the task is well-scoped and low-risk.
4. Grok Build requires authentication (browser login or `XAI_API_KEY`).
5. This skill is only valid for the version of Grok Build documented at https://docs.x.ai/build/ in May 2026.

## Limitations (Strictly Based on Docs)

- Headless mode is primarily triggered with `-p`.
- ACP is available via `grok agent stdio`.
- No native multi-worktree management or skill marketplace commands are covered in this skill unless explicitly documented.
- Enterprise deployment features are not included.

## Recommended Pattern in Hermes

When the user wants to delegate work to Grok Build:

1. Hermes generates a clear, self-contained prompt.
2. Hermes calls the `terminal` tool with the appropriate `grok -p ...` command.
3. For very long tasks, use `background=true` + `notify_on_complete=true`.

This approach keeps Hermes as the orchestrator while leveraging Grok Build's agent capabilities.