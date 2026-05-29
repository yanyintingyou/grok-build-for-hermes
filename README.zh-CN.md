# grok-build-for-hermes

简体中文 | [English](README.md)

这是一个 Hermes skill，允许 Hermes 调用和编排 xAI 官方的 **Grok Build CLI**。

**适配的 Grok Build 版本**：`@xai-official/grok@0.1.220` (npm) / `grok-build-0.1` (模型) — 截至 2026 年 5 月，处于 early beta 阶段。

## 跨 Agent 兼容结构与安装

本仓库现在使用双布局 skill 结构：

```text
repo/
├── SKILL.md                                      # 跨 Agent 通用的权威说明
├── AGENTS.md                                    # Codex / OpenAI 项目指令
├── CLAUDE.md                                    # Claude Code 项目指令
├── agents/openai.yaml                           # 可选 OpenAI agent 元数据
└── skills/autonomous-ai-agents/grok-build-for-hermes/SKILL.md  # Hermes / OpenClaw 打包版 skill
```

根目录 `SKILL.md` 与 `skills/autonomous-ai-agents/grok-build-for-hermes/SKILL.md` 会保持字节级一致。若有脚本、参考文档或依赖文件，它们也会同时出现在两个可安装位置旁边。

### 作为可复用 skill 安装

- **Claude Code**：复制 `skills/autonomous-ai-agents/grok-build-for-hermes/` 到 `~/.claude/skills/autonomous-ai-agents/grok-build-for-hermes/`。
- **OpenAI Codex / OpenAI agents**：在本仓库工作时保留 `AGENTS.md`；如需作为可复用 skill，复制 `skills/autonomous-ai-agents/grok-build-for-hermes/` 到 `~/.agents/skills/autonomous-ai-agents/grok-build-for-hermes/`。
- **OpenClaw**：复制 `skills/autonomous-ai-agents/grok-build-for-hermes/` 到 `<workspace>/skills/autonomous-ai-agents/grok-build-for-hermes/`、`<workspace>/.agents/skills/autonomous-ai-agents/grok-build-for-hermes/` 或 `~/.openclaw/skills/autonomous-ai-agents/grok-build-for-hermes/`。
- **Hermes Agent**：复制 `skills/autonomous-ai-agents/grok-build-for-hermes/` 到 `~/.hermes/skills/autonomous-ai-agents/grok-build-for-hermes/`，然后开启新的 Hermes 会话。
- **通用 AgentSkills 加载器**：使用包含 `SKILL.md` 的目录；skill 名称为 `grok-build-for-hermes`。


本 skill 严格基于 https://docs.x.ai/build/ 的官方文档（截至 2026 年 5 月）。仅记录当时官方文档中明确存在的功能。

## 动因

虽然 Hermes 可以通过 `xai-oauth` 提供商直接使用 Grok 模型，但很多用户也希望利用 **Grok Build** 完整 coding agent 的能力（Plan 模式、Skills、Headless 执行、ACP 支持等）。

本 skill 提供了一种干净的方式，让 Hermes 可以调用 `grok` 二进制，支持 headless 模式或 ACP 模式，类似于 `codex` skill 调用 OpenAI Codex CLI 的方式。

## 官方文档支持的关键能力

- Headless 执行：`grok -p "prompt"`
- 输出格式：`plain`、`json`、`streaming-json`
- Always-approve 模式
- ACP 支持（`grok agent stdio`）
- 会话管理参数（`-s`、`-r`、`-c`）
- 模型选择（`-m`）

## 安装

添加本仓库为 skill tap 后：

```bash
hermes skills tap add yanyintingyou/grok-build-for-hermes
hermes skills install yanyintingyou/grok-build-for-hermes/grok-build-for-hermes --force
```

## 使用示例

对 Hermes 说：

```text
使用 grok-build-for-hermes 以 headless 模式运行以下任务：
任务：将用户认证模块重构为使用 JWT。
工作目录：~/projects/my-app
```

Hermes 会生成符合官方文档参数的 `terminal` 调用。

## 版本说明

本 skill 适配 **Grok Build `@xai-official/grok@0.1.220`**（截至 2026 年 5 月的 npm 最新版本），使用模型 `grok-build-0.1`。相关内容基于 https://docs.x.ai/build/ 截至 2026 年 5 月的文档。未来版本可能引入本 skill 未覆盖的变更。

## License

MIT License.