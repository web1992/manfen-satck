---
name: setup-pstack
description: "配置 pstack 各角色使用的模型及推理预算。检测你可用的模型，并写入一条始终应用的规则以覆盖 skill 默认值。用于 /setup-pstack、「configure pstack models」、「pstack budget」，或更改 pstack 的模型选择。"
---

# Setup pstack

写入 `~/.cursor/rules/pstack-models.mdc`，这是一条始终应用的规则，用于按角色设置 pstack 的模型。

## Steps

### 1. Detect available models

枚举本会话里可传给 `Task` subagent 的 model slug。这是最可靠来源。若 Cursor 还暴露了列出用户 entitled 模型的 models API 或 CLI，优先用它以求完整。若检测不到任何 slug，请用户粘贴他们有权限的 slug。切勿写入你未确认可用的真实 slug。别名 `inherit-parent` 和 `auto` 始终有效，尽管它们不是被检测到的 slug。

### 2. Load current state

默认的角色到模型映射即下文步骤 5 中的规则形态。若 `~/.cursor/rules/pstack-models.mdc` 已存在，读取它，并将其 `# budget` 行与各角色取值视为当前选择。否则从那些默认值开始。步骤 5 中未出现的角色行（例如 `how critics`）来自已废弃角色，应删除。

### 3. Budget, map, and confirm

**(a) 询问预算。** 优先用 AskQuestion，而非自由文本。用下列确切标签提供四个选项，若规则中已有预算则点名当前预算。无规则时说明 `large` 与 skill 默认一致。

- `unlimited — max reasoning`
- `large — xhigh reasoning`
- `medium — high reasoning`
- `small — medium reasoning`

**(b) 应用预算。** 从 skill 默认值构建工作表；重跑时保留你按 family、list 或别名（`inherit-parent`、`auto`）改过的任意角色。`unlimited`、`large`、`medium`、`small` 将每个真实 slug（含 panel 条目）的 effort token 设为 `max`、`xhigh`、`high` 或 `medium`。effort token 是阶梯 `max` > `xhigh` > `high` > `medium` > `low` 上的最后一个 token，或在末尾有 `fast` 时为其前一个。若结果不是被检测到的 slug，则使用该 family 中 effort 不高于目标、且已被检测到的最高 effort 的 slug；否则将该角色标为需要选择。`inherit-parent` 与 `auto` 不变。因此 `unlimited` 会把 `claude-opus-5-5-xhigh` 变成 `claude-opus-5-5-max`。Grok slug 上限为 `xhigh`，故在 `unlimited` 下回退会把 Grok 设在 `xhigh`，并保持 `grok-4.7-xhigh-fast` 不变。`large` 保留两处默认。`small` 会将它们变为 `claude-opus-5-5-medium` 与 `grok-4.7-medium-fast`。

**(c) 展示角色并确认。** 展示每个角色及其模型，将不在检测集合中的真实 slug 标为需要选择。同时列出步骤 2 删掉的每一行。询问是否按现状接受，或修改特定角色；选项为检测到的模型，以及 `inherit-parent` 和 `auto`（二者均表示：该角色在父聊天模型上运行，Auto 用户借此保持 Auto）。优先 AskQuestion。对 panel 角色（arena runners、architect runners、interrogate reviewers），值为列表，每个条目各起一个 subagent（含别名条目），列表长度决定数量。`arena cross-judge pool` 也是列表，但 Arena 只跑一个 judge。它按列表顺序仅尝试与父模型不同 family 的值，故按偏好排序。`inherit-parent` 或 `auto` 的值永不担任 judge。`swarm workers` 是每个 worker 的默认模型，除非 race 或 comparison 为每条 arm 指定另一模型。

### 4. Validate

写入的每个真实 slug 必须在检测集合内。`inherit-parent` 与 `auto` 始终通过。若所选真实 slug 不可用，停止并再次询问。

### 5. Write the rule

写入 `~/.cursor/rules/pstack-models.mdc`，设置 `alwaysApply: true`，一条含所选标签及目标 effort 的 `# budget` 行，以及每个角色一行，标签与 poteto-mode 所用相同。覆盖整个文件以使重跑幂等。形态如下：

```
---
description: pstack per-role model choices (overrides skill defaults)
alwaysApply: true
---
# pstack model configuration. One line per role. Delete a line to fall back to the skill default.
# `inherit-parent` or `auto` as a value: the role runs on the parent chat model (omit Task `model`). Alias entries in a panel list still count toward its fan-out.
# budget: large (xhigh)
feature, refactoring: grok-4.7-xhigh-fast
bug-fix: grok-4.7-xhigh-fast
perf-issue: grok-4.7-xhigh-fast
hillclimb: grok-4.7-xhigh-fast
judgment and prose: claude-opus-5-5-xhigh
hardest tasks: claude-opus-5-5-xhigh
how explorer: grok-4.7-xhigh-fast
how explainer: claude-opus-5-5-xhigh
why investigators: grok-4.7-xhigh-fast
why synthesizer: claude-opus-5-5-xhigh
reflect tooling: grok-4.7-xhigh-fast
reflect judgment, divergent, synthesizer: claude-opus-5-5-xhigh
arena runners: claude-opus-5-5-xhigh, grok-4.7-xhigh-fast
arena cross-judge pool: claude-opus-5-5-xhigh, grok-4.7-xhigh-fast
swarm workers: grok-4.7-xhigh-fast
architect runners: claude-opus-5-5-xhigh, grok-4.7-xhigh-fast
interrogate reviewers: claude-opus-5-5-xhigh, grok-4.7-xhigh-fast
```

### 6. Confirm

告知用户规则已写入，且对新会话生效。重跑本 skill 会更新该规则。

### 7. Offer a verification skill (optional)

检查项目是否有驱动真实应用以作佐证的方式（`verify-*` skill，或既有 harness）。若没有，一次性提议："want a project-local verification skill, so agents can drive the app the way a user does and prove changes work? I can generate one with /create-verification-skill." 若同意，调用 `/create-verification-skill`（在 pstack 安装处解析：workspace、user 或 plugin）。若拒绝，不再追问。

### 8. Offer a formal verification skill (optional)

检查项目是否已有 formal verification skill（在代码上跑 model checker、证明或其他 verifier 的项目 skill）。若没有，一次性提议："want a formal verification skill, so agents can prove a key property of your code holds in every case in scope, not only the cases tests reach? I can generate one with /create-formal-verification-skill." 若同意，调用 `/create-formal-verification-skill`（在 pstack 安装处解析：workspace、user 或 plugin）。若拒绝，不再追问。
