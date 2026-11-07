---
name: show-me-your-work
description: "为长时间或无人值守的工作保留可审查的决策轨迹：TSV 日志，每个决策一行（做了什么、为何、证据、结果）。默认仅本地；当审查者需要轨迹才能信任结果时再 commit。用于 /show-me-your-work、自主或多阶段运行，或用户离开后再来审查的工作。"
disable-model-invocation: true
---

# Show me your work

只维护一份权威日志。

## The format

单个 TSV 文件，每个决策一行。单元格保持单行。evidence 是指针，不是散文。

复制 `references/decision-log-template.tsv`（表头行）以开始干净日志。列如下：

- **ts。** ISO8601 时间戳。
- **phase。** 阶段或工作流。
- **decision。** 选了什么或做了什么，一行。
- **why。** 用平实语言写理由。若由某条原则驱动，直白说出，不要写成行话标签。
- **evidence。** 能证明它的链接或路径：commit SHA、PR 编号、`file:line`，或 artifact、trace、截图路径。不要写段落。
- **result。** 结果或谓词状态：`tests green`、`reverted`、`pixel-diff 0`、`INCONCLUSIVE`、`open`。

下面是一个例子，口吻平实，审查者一眼能读。

```
ts	phase	decision	why	evidence	result
2026-05-24T09:02:00Z	frame	counted the work first, about 100 components and roughly 75 hours	wanted to know the size before starting a long run	commit 3a9f1c2	found 5 things to sort out before starting
2026-05-24T09:40:00Z	harness	took screenshots of the old version before changing anything	so we can compare old against new and catch any visual change	scripts/snapshot.sh, baseline/	saved 120 reference screenshots
2026-05-24T11:15:00Z	widget	moved the widget styles over without changing how it looks	keep the change small and the result identical	commit 7c21e0a, pixel-diff 0	looks identical, tests pass
2026-05-24T12:30:00Z	widget	threw out a helper's work because its screenshots were blank	checked the real files instead of trusting its summary	worktree reset	reverted, tightened the instructions for next time
```

## Logging a row

每条记录按你会向队友交代做了什么那样写。平实用语、具体动作，不要 AI 腔或抽象行话（**unslop** skill 也适用于日志正文）。

使用辅助脚本 `scripts/log.sh <logfile> <phase> <decision> <why> <evidence> <result>`。它会打上 `ts`，首次使用时写入表头，剥离多余 tab/换行，并对以 `=`、`+`、`-` 或 `@` 开头的单元格加单引号前缀。裸 `printf` 追加一行也行，但若单元格来自生成或用户输入，同样注意这些字节。

只记录决策点与检查点，而非每个动作：选了哪条分支、某单元完成及其验证结果、因何 pivot 或 revert、暴露的 blocker、修复的门禁。循环运行时，每次迭代一行。琐碎且不言自明的跳过。

一次 run 指一个 agent 对话，含后续轮次及其摘要。一次接手、替换 agent 或新聊天开始新的 run。当某 run 往已有行的日志追加时，其第一行 phase 为 `start`；在另一 run 的 `start` 行之后亦然。因此某 run 在后续轮次回到日志时，先读日志末尾几行，看是否有别的 run 写过。`start` 行标明它之前、本 run 未写的那批行的 `ts` 范围，其 evidence 标明本 run，例如 agent id。`start` 不要用于别的用途。

## Where it lives

默认日志是工作 artifact，不 commit。放在工作目录的 `decisions.tsv`，或多项并行时用 `.audit/<task-slug>.tsv`，且不纳入 git。

仅当工作足够艰巨、审查者需要轨迹才能信任结果时才 commit。

## Rules

- 仅追加。错误判断用新行覆盖，不要编辑或删除历史。
- 优先使用已 commit 脚本产出的证据，而非手工一次性做法（**encode-lessons-in-structure** 原则 skill）。

## Audit the log against the transcript

run 结束前、交还之前，核对日志是否说真话。在当前工作区 `agent-transcripts/` 下读取本 run 的 transcript（系统 prompt 会给出路径）。不要 glob `~/.cursor/projects/*/` 下无关的私人聊天。按实际发生的事逐行核对本 run 的记录。每段从本 run 的某个 `start` 行开始，若本 run 创建了日志则从第一行开始，到另一 run 的下一条 `start` 行结束：

- 核对每一行对应真实决策或动作。
- 核对每行 evidence 可解析且支撑该行声称的内容。
- 塑造了工作却未记录的 fork、pivot 或放弃的路线是缺口，补上。

修正日志，不要改故事。审计永远不编辑或删除行，即便是捏造的行。当某行既非真实决策也非真实动作，或声称/evidence 有误时，追加一行覆盖旧记录，写明实际发生的事和可解析的指针。本审计不检查本 run 段以外的行；若本 run 的工作显示其中某行有误，像任何错误判断一样用新行覆盖。

## Cross-model review of the trail

交还之前，用与干活所用模型不同模型族的 subagent 拉起审查。自审不能替代。subagent 读审计轨迹与本 run 的 transcript，标出用户应留意之处。不是重做工作，而是扫描次优或有风险之处。按 **poteto-mode** skill 的 Subagents 一节，为每个 subagent 选择推理等级以及 fast / standard 速度。

- 证据薄弱或缺失的已记录决策。
- 跳过或未在 transcript 中证明的验证步骤。
- 事后看有风险的选择（过早、范围蔓延、掩盖症状）。
- 用户随手扫一眼会错过的缺口。

凡产生轨迹的 run，每次回复以 “Attention” 段结尾。单独一行写审查者模型（`reviewed by <model>`），再列出每条 flag，指向具体行或时刻。“No flags” 是合法取值。模型名本身不算 flag。

## Reviewing the trail

自上而下读，顺着 evidence 指针，抽查。GitHub 会把已 commit 的 TSV 渲染成表格。终端可用 `column -s$'\t' -t decisions.tsv` 渲染。

## Composing this skill

其他 skill 把审计轨迹路由到这里，而不是自造格式。按名称引用，由本 skill 拥有格式。不要复述各列。
