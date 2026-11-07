---
name: why
description: "用于「为什么 X 是这样实现的」「为什么选 Y」、设计理由、回归、事后复盘或数据支撑的阈值。发现可用 MCP，并行查询各证据类别（源码管理、issue tracker、长文档、即时聊天、基础设施可观测性、错误追踪、产品分析数仓），再返回带引用的决策与权衡说明。运行时行为用 how。"
disable-model-invocation: true
---

# Why

调查代码背后的动机与意图。配套的 `how` skill 回答代码做什么、怎么工作。`why` 回答是什么力量塑造了它的形态。

下面每次 spawn 对应 `pstack-models.mdc` 规则里的一行角色及默认值。把 `model` 设为该行的值；规则或该行缺失则用默认。值为 `auto` 或 `inherit-parent` 时不设 `model`。若 Task 工具拒绝某个 slug，改用默认并说明。若默认也被拒绝，从其错误信息里选同系列最接近的合法 slug。按 **poteto-mode** skill 的 Subagents 一节，为每个子 agent 选择推理等级以及 fast / standard 速度。

每次 spawn 为 `generalPurpose` 且 `readonly: false`（agent 模式）。**不要用 readonly / Ask 模式。** 那会去掉 MCP 访问，使基于 MCP 的 investigator 与 synthesizer 的引用抽查无法工作。investigator 仍不应写入任何内容。

## Operating Posture

以**谨慎、保守、精确的 investigator** 姿态工作。诚实区分已知与推断。阅读 `references/epistemics.md` 获取完整置信度框架与措辞指南。synthesizer 必须遵守。

## Step 1. Understand the Target and the Question

**target** 通常是代码块、模式、特性或具名设计决策。**question** 通常是设计理由、权衡、驱动的边界情况、外部约束、死代码，或大范围历史梳理。

若 target 含糊（「为什么这样做？」没有明确指代），从对话上下文（打开的文件、近期编辑、光标位置、刚讨论的内容）做最佳猜测。简短说明你的理解，便于用户纠正后再继续。

## Step 2. Establish the Code Anchor

spawn investigator 之前，内联构建种子上下文：文件路径与行范围、关键符号（函数、类、常量）、最近触及 target 的若干 commit、merge commit 中的 PR 编号（主题行中的 `(#1234)` 模式），以及关联的 ticket ID。对末次触及的 commit 跑 `git blame -L <start>,<end> <file>`，对含 patch 与重命名的完整历史跑 `git log --follow -p -- <file>`，对近期 commit（可见 PR 号）跑 `git log --oneline -20 -- <file>`，提取 PR 编号跑 `git log -1 --format=%B <commit>`。然后拉取有实质内容的 commit 的 PR 正文与讨论：`gh pr view <number> --json title,body,author,createdAt,mergedAt,labels,closingIssuesReferences,comments,reviews`。该 anchor 发给每个 investigator 和 synthesizer。

## Step 3. Spawn Parallel Investigators

**默认做完整并行调查。** 目标是覆盖图完整，不是最小化。记录空结果，别跳过搜索。

### Discovery

spawn investigator 之前，列出 Cursor 环境中可用的 MCP。有 available-tools map 就用；否则查看 Cursor 暴露的 `mcps/` 目录里已启用的 MCP server。

把每个可用 MCP 映射到一个证据类别：

1. Source control history
2. Issue / ticket tracker
3. Long-form documents
4. Real-time team chat
5. Infrastructure observability
6. Error / exception tracking
7. Product analytics warehouse

源码管理始终通过 git 与 `gh` 可用。对其余六类，用 MCP 名称、server 说明、工具名与资源描述分类。若一个 MCP 可归属多类，选与其主要证据匹配的一类。模糊情况记入 coverage map。

每个有匹配 MCP 的类别 spawn 一个 investigator，同一条消息里并发运行，每人恰拥有一种工具或 MCP。别让一个 agent 覆盖多个 MCP。`model`：`why investigators` 行，默认 `grok-4.7-xhigh-fast`。

每个 investigator 的 prompt 为填好的 `references/investigator-prompt.md`，外加：
- 所选 MCP 对应的类别 playbook `references/sources/<source>.md`，改编自 `references/source-playbook.md` 中的示例
- 若 target 代码看起来偏防御性（null 检查、重试逻辑、超时处理、限流、feature flag、出口守卫、OOM handler），加上 `references/sources/incident-postmortem.md`
- code anchor 与用户原始问题

### Investigator roster, one per available evidence category

下面每个类别独特地能呈现一种「why」形态，列在「Best at」之后。用它了解该期待什么回报、某类返回空时如何命名缺口，且仅在可证无关的罕见情况下 justify 跳过。

1. **Source control**（git 历史、PR 用 `gh`、代码注释、测试）。始终 spawn，唯一保证来源。Best at *review 阶段留下的实现期理由*。
2. **Issue / ticket tracker**（如 Linear、Jira、GitHub Issues、Plane、Shortcut MCP）。Best at *产品或业务上的 forcing function*。why 在工程外时最强。
3. **Long-form documents**（如 Notion、Confluence、Google Docs、Coda MCP）。Best at *成文的长设计理由*，写在变成代码之前。
4. **Real-time team chat**（如 Slack、Discord、Microsoft Teams、Mattermost MCP）。Best at *从未沉淀成文档的实时审议*。源码、ticket、文档纸面痕迹薄时尤其重要。
5. **Infrastructure observability**（如 Datadog、New Relic、Honeycomb、Grafana、Splunk MCP）。Best at *促使写码的基础设施与运行时现实*。target 响应基础设施信号（超时、重试、限流、断路器）时最强。
6. **Error / exception tracking**（如 Sentry、Rollbar、Bugsnag、Airbrake MCP）。Best at *催生防御或修正代码的具体异常与错误轨迹*。catch 块、null 守卫、类型检查、重试等防御代码时最强。
7. **Product analytics warehouse**（如 Databricks、Snowflake、BigQuery、ClickHouse、dbt、Redshift MCP）。Best at *塑造代码的产品与数据现实*。flag 门控代码、实验驱动上线、数据迁移、「这个数字从哪来」类问题时最强。

### When to skip an investigator

仅在有**明确书面理由**、且写入最终 Sources Consulted 时跳过。两种有效理由：

- **本环境没有该类的 MCP**。标为缺口，不是选择：「Real-time team chat skipped. No matching MCP available, so the conversational record was not searchable.」
- **来源可证无关**，不是「大概无关」。门槛要高：「Error / exception tracking skipped. Target is a build-time script with no runtime code path.」

若范围评估表明是单 commit 的琐碎 target，且 PR 描述已含完整答案，可在确认七类可用搜索都会冗余后**仅内联作答**。明确说明。应罕见。

## Step 4. Synthesize

spawn 一个 synthesizer。`model`：`why synthesizer` 行，默认 `claude-opus-5-5-xhigh`。它获得：
1. investigator 发现，含空结果与带理由跳过的类别
2. code anchor
3. 用户原始问题
4. `references/epistemics.md` 中的 epistemics 框架
5. `references/synthesizer-prompt.md` 中的 synthesizer prompt 模板

## Step 5. Present

向用户呈现 synthesizer 输出。可为清晰或补对话上下文做轻度编辑，但**不要改写置信度措辞**。

## Output Format

结构见 `references/synthesizer-prompt.md`：The Question、The Code in Question、What We Found、What We Can Reasonably Infer、Competing Hypotheses、What We Don't Know、Sources Consulted、Confidence Summary。按需调整，但保持置信度分层，Sources Consulted 每个 investigator 一行，含无结果或跳过的及原因。

Sources Consulted 之后，若用户的 `why` 问题是改这段代码的前奏，把谱系发现转成 Preserve / Change / Avoid / Risk 约束集，供规划变更用。

## Common Failure Modes to Avoid

- **Recency bias**。假定最近 commit 即为权威。当前形态往往是许多早期决策的累积。往回追。
