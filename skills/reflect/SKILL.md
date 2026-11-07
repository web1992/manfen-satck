---
name: reflect
description: "对当前 transcript 并行 spawn 三个 review subagent，提炼可沉淀经验，并把每条路由到既有 skill 上的具体编辑。用户说 reflect 时使用。"
disable-model-invocation: true
---

# Reflect

从当前对话挖掘可持久化的经验，再路由进 skill 编辑。

用户说 "reflect" 或 "/reflect" 时 invoke。对话琐碎、离题，或父 agent 已正确遵循既有 skill 时跳过。一次性事件不是 learning。

## 1. Locate the active transcript

fan out 前，在系统 prompt 指名的 `agent-transcripts/` 目录找到本会话的 transcript。不要 glob `~/.cursor/projects/*/`——会跨越工作区并读到无关项目的私密聊天。

```bash
ls -t <agent-transcripts>/*.jsonl <agent-transcripts>/*/*.jsonl <agent-transcripts>/*/subagents/*.jsonl 2>/dev/null | head -10
```

覆盖旧版扁平（`<id>.jsonl`）、现行嵌套（`<id>/<id>.jsonl`）与 subagent（`<parent>/subagents/<child>.jsonl`）布局。对每个候选读 JSONL 首行。取 `message.content[0].text` 含会话 opening user prompt 的文件。若无匹配，改传紧凑的 session digest。

## 2. Spawn three reviewers in parallel

一条消息三次 `Task` 调用，`subagent_type: generalPurpose`，agent 模式（`readonly: false`）。reviewer 需 MCP 查 transcript 引用的 ticket、聊天线程与可观测 trace；readonly 会剥掉 MCP。

每个 reviewer 与 synthesizer 在 `pstack-models.mdc` 规则中指定 role 行与 default。`model` 设为该行值；规则或行缺失则用 default。值为 `auto` 或 `inherit-parent` 时不设 `model`。Task 工具拒绝 slug 时用 default 并说明；default 也被拒则从错误信息选同家族最接近合法 slug。按 **poteto-mode** skill 的 Subagents 一节为每个 subagent 选推理等级与 fast / standard 速度。

| Lens | Role line | Default `model` | Prompt template |
|---|---|---|---|
| Judgment | `reflect judgment, divergent, synthesizer` | `claude-opus-5-5-xhigh` | `references/judgment-reviewer.md` |
| Tooling | `reflect tooling` | `grok-4.7-xhigh-fast` | `references/tooling-reviewer.md` |
| Divergent | `reflect judgment, divergent, synthesizer` | `claude-opus-5-5-xhigh` | `references/divergent-reviewer.md` |

各 template 原样传入，在标记处替换 transcript 路径或 digest。reviewer 在 `Task` 响应体中返回 findings。

## 3. Synthesize

一次 `Task` 调用，`subagent_type: generalPurpose`，`model` 来自 `reflect judgment, divergent, synthesizer` 行（default `claude-opus-5-5-xhigh`），agent 模式（`readonly: false`）。通过 MCP 做点验引用；readonly 会剥掉 MCP。`references/synthesizer.md` 原样传入，在标记处内联各 reviewer 完整输出。返回 Accepted / Rejected / Backlog 列表。

## 4. Structural enforcement check

若某 Accepted 项可由 lint 规则、脚本、元数据标志或运行时检查更可靠地 enforce，移到 Backlog。见 **encode-lessons-in-structure** principle skill。

## 5. Apply

呈现 synthesizer 完整的 Accepted / Rejected / Backlog 输出，在应用任何 Accepted 编辑前等待明确批准。用户挑选子集并可重定向 routing。skill 变更影响组织内每个未来 agent。不要自动应用。

Backlog 各项直接记入团队 devex 或 backlog tracker，无需等待。只有 Accepted 列表等批准。

绝不编辑 `plugins/cache/` 下的 skill。批准前展示目标：`.cursor/skills/<skill>/` 或 `~/.cursor/skills/<skill>/`。批准后把 skill 文件夹拷到该处并编辑副本。不要开 PR 或 push。

严格按每条批准行的 Routing：

- 琐碎既有 skill 编辑（一行 bullet、收紧句子、纠正过时事实）：父 agent 直接做。
- 实质性既有 skill 编辑（新 section、新 pattern 表、超过约 10 行）：交给 Cursor 内置 `create-skill` skill，跑其 draft / test / iterate 循环。
- `tune description: <skill path>`（skill 存在但未在应触发时触发）：交给 `create-skill`，跑其 description-optimization 循环。
- `new skill via create-skill: <kebab-name>`：创建交给 `create-skill`。不要即兴发明形态。

若环境提供 SKILL.md validator，在每个触及的 skill 上跑完再宣告完成。

## 6. Summarize for the user

短列表，无前言：

- 已应用编辑：`<skill path>`。每项一行说明改了什么。
- 新建 skill：`<skill path>`。每项一行（少见）。
- 已记入 devex tracker 的 Backlog：`<issue title>`（`<tags>`）。每项一行。
- 已丢弃：每条 rejected finding 一行 + synthesizer 给出的原因。
