# grok-build-for-hermes

[简体中文](README.zh-CN.md) | English

A Hermes skill that allows Hermes to invoke and orchestrate the official **Grok Build CLI** from xAI.

**Target Grok Build version**: `@xai-official/grok@0.1.220` (npm) / `grok-build-0.1` (model) — early beta as of May 2026.

## Cross-agent compatibility and installation

This repository now uses a dual-layout skill structure:

```text
repo/
├── SKILL.md                                      # canonical cross-agent instructions
├── AGENTS.md                                    # Codex/OpenAI project instructions
├── CLAUDE.md                                    # Claude Code project instructions
├── agents/openai.yaml                           # optional OpenAI agent metadata
└── skills/autonomous-ai-agents/grok-build-for-hermes/SKILL.md  # Hermes/OpenClaw packaged skill copy
```

The root `SKILL.md` and the packaged `skills/autonomous-ai-agents/grok-build-for-hermes/SKILL.md` are intentionally byte-identical. Supporting files, when present, are available next to both copies.

### Install as a reusable skill

- **Claude Code**: copy `skills/autonomous-ai-agents/grok-build-for-hermes/` to `~/.claude/skills/autonomous-ai-agents/grok-build-for-hermes/`.
- **OpenAI Codex / OpenAI agents**: keep `AGENTS.md` when working in this repository, or copy `skills/autonomous-ai-agents/grok-build-for-hermes/` to `~/.agents/skills/autonomous-ai-agents/grok-build-for-hermes/` for a reusable skill.
- **OpenClaw**: copy `skills/autonomous-ai-agents/grok-build-for-hermes/` to either `<workspace>/skills/autonomous-ai-agents/grok-build-for-hermes/`, `<workspace>/.agents/skills/autonomous-ai-agents/grok-build-for-hermes/`, or `~/.openclaw/skills/autonomous-ai-agents/grok-build-for-hermes/`.
- **Hermes Agent**: copy `skills/autonomous-ai-agents/grok-build-for-hermes/` to `~/.hermes/skills/autonomous-ai-agents/grok-build-for-hermes/`, then start a new Hermes session.
- **Generic AgentSkills loaders**: use the directory that contains `SKILL.md`; the skill name is `grok-build-for-hermes`.


This skill is strictly based on the official documentation at https://docs.x.ai/build/ (as of May 2026). It only documents features explicitly present in the official docs at that time.

## Motivation

While Hermes can use Grok models directly via the `xai-oauth` provider, many users also want to leverage the full **Grok Build** coding agent experience (plan mode, skills, headless execution, ACP support, etc.).

This skill provides a clean way for Hermes to call the `grok` binary in headless mode or ACP mode, similar to how the `codex` skill works with OpenAI Codex CLI.

## Key Capabilities (from Official Docs)

- Headless execution: `grok -p "prompt"`
- Output formats: `plain`, `json`, `streaming-json`
- Always-approve mode
- ACP support (`grok agent stdio`)
- Session management flags (`-s`, `-r`, `-c`)
- Model selection (`-m`)

## Installation

After adding this repository as a skill tap:

```bash
hermes skills tap add yanyintingyou/grok-build-for-hermes
hermes skills install yanyintingyou/grok-build-for-hermes/grok-build-for-hermes --force
```

## Usage Example

Ask Hermes:

```text
Use grok-build-for-hermes to run the following task with Grok Build in headless mode:
Task: Refactor the user authentication module to use JWT.
Working directory: ~/projects/my-app
```

Hermes will generate an appropriate `terminal` call using the documented flags.

## Version Notice

This skill targets **Grok Build `@xai-official/grok@0.1.220`** (latest npm version as of May 2026), with model `grok-build-0.1`. It is based on the documentation at https://docs.x.ai/build/ as of May 2026. Future versions may introduce breaking changes not covered here.

## License

MIT License.