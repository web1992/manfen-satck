---
name: recall
description: "从自有聊天记录、现场状态与共享记录（用户反馈、既往修复、事故）重建近期工作上下文，交回紧凑的当前状态摘要。用于 'recall my work on X'、'catch me up'、'what have I been working on'、'where did I leave off'，在开始或恢复工作前。"
disable-model-invocation: true
---

# Recall

**在开始或恢复工作前，重建用户近期工作上下文，交回紧凑胶囊：现状与下一步。**

保持紧凑、紧扣主题。只读范围内线程所需内容，然后停。

上下文分布在两份记录里。自有聊天历史保存你做过的与拍板的；共享记录保存同一批代码下以其他名义发生的一切：用户反复报的症状、已 ship 又被 revert 的修复、prod 仍在触发的错误。第二份记录是 **why** skill 检索的对象，覆盖源码控制、issue tracker、聊天与 issue 频道、长文档、错误追踪。长尾 bug 多的特性，故事多半在那里，不要只从 transcript 重建。

Transcript 位于 `~/.cursor/projects/<slug>/agent-transcripts/<uuid>/<uuid>.jsonl`，其中 `<slug>` 为工作区路径去掉 leading slash、每个 `/` 换成 `-`（如 `/Users/you/proj` → `Users-you-proj`）。每行一条聊天消息。

1. 分类再路由。要恢复的某次具体先前聊天走 `session-pickup` playbook，不是本 skill。把习惯固化成持久 skill 是 `automate-me`。给人可读的工作摘要则是另一任务。Recall 在行动前跨近期聊天加载工作上下文。若用户已给出完整状态胶囊（路径、branch、变更），直接使用并跳过挖掘。
2. 搜索前锁定范围。钉住时间窗（「近期」是真实区间，默认最近 7 天）、若指明的主题、工作区（默认当前；除非被要求，绝不读其他项目的 transcript）。把范围复述给用户。绝不悄悄把「全部」变成「最近 N」。
3. 向聊天历史 fan out。用 fast、便宜的 model 并行 spawn subagent，各负责语料一切片。告知每个 subagent：按真实修改时间排序（`ls -t`），绝不按 UUID 名；先 grep 主题再只读匹配聊天及其相关片段；跳过当前聊天与明显噪声（subagent、eval、test 聊天）。各返回同一 schema，每个聊天一块：主题、用户目标、决策、开放线程、卡点与纠正、artifact（PR、ticket、branch），并引用聊天 UUID。仅一两场聊天时跳过 fan-out，直接搜。原始 transcript 留在 subagent；主线程只收发现。按 **poteto-mode** skill 的 Subagents 一节为每个 subagent 选推理等级与 fast / standard 速度。
4. 只要主题点名特性、文件、子系统、区域或 bug，就扫共享记录。这是默认，不是判断题；「我在 X 上的工作」也不能豁免。交给 **why** skill 的 source investigators，但把问题从「为何这样建」改为侧重「当前状态、试过什么没稳住、用户还在报什么」。复用其 per-source playbook，与聊天历史挖掘并行跑 investigators，继承其姿态：每源一个 investigator、null 也是发现、不可用 MCP 则跳过并说明。把返回折进简报。仅纯活动回忆且无命名目标（「我这周做了什么」）时跳过，此时自有历史与现场状态即全部答案。
5. 对照现场状态验证。对挖掘与 sweep 浮出的 PR、branch、ticket 用 `git` 与 `gh` 核对。当答案取决于 agent 实际做了什么（跑过的工具、读过的文件、遇到的错误）时，读完整 transcript，不要只看裁剪过的本地副本。
6. 按下文契约写简报。按线程分组，紧扣命名主题。

## Output contract

先胶囊，再线程状态，再问题，再下一步。更深细节放下面或删掉。

- **胶囊。** 最多 5 条。这项工作是什么、整体所处位置。
- **线程。** 每条一行，前缀恰好一个状态 tag：`[merged #N]`、`[open PR #N]`、`[in flight <branch>]`、`[verified, uncommitted]`、`[reverted #N]`、`[planned, not started]`。无 tag 的线程视为未完成，须打 tag。
- **问题。** 最多 5 条，重复出现的那类。包含用户反复报的症状及 ship 后又 revert 的修复，使下次尝试从上一次失败处起跑。
- **下一步。** 单一最有用的下一步，具体可执行。

不阻塞本工作的相邻特性或 ticket 不写进简报。胶囊与线程行超出一屏时，先砍细节再砍线程。通过 **unslop** skill 写简报；聊天发现用 UUID 引用，共享记录发现用其来源（PR #、ticket ID、聊天 permalink、错误追踪 issue）；任何公开输出前脱敏私密上下文。

**回复：** 按上契约的简报。
