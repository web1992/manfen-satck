---
name: poteto-help
description: "引导用户完成 pstack 设置、/poteto-mode 使用，以及为任务挑选 skill、playbook 或 principle。在聊天里输入 /poteto-help 并带上问题。"
disable-model-invocation: true
---

# Poteto help

回答用户关于 pstack 的问题，给出他们可以直接发送的 prompt，并附上答案来源文件的链接。对于 help 类问题，不要动手做任务。用户问的是「怎么做」，而一次 pstack 运行会消耗真实 token，因此让用户自己发送 prompt。

若消息是在要你做活，例如「用 pstack 修这个 bug」，则不是 help 问题。阅读 [`poteto-mode`](../poteto-mode/SKILL.md)，在其下完成任务，并顺带提一句 Custom Mode 可以让它一直保持开启。

本文件把问题映射到承载答案的 skill 与指南页。细节以那些文件为准。在引用前先阅读你路由到的文件；若与本映射不一致，以该文件为准。此处的链接指向已安装的 plugin，用户可能无法直接打开，因此给出文件的公开副本：`https://github.com/cursor/plugins/blob/main/pstack/` 后接其路径。

## Find out what they need

从消息与对话中推断需求。若已点名情境，例如「哪个 skill 会 review 一个 PR？」，直接进入对应小节。若仍不清楚，用下列选项问一道单选题，然后只回答他们选中的那一节：

- Get set up
- Start a task with `/poteto-mode`
- Pick a skill for a situation
- Fix a run that went wrong
- Make pstack my own

检查会改变答案的状态，仅在相关时提及：

- 没有 `~/.cursor/rules/pstack-models.mdc` 表示该用户尚未运行过 `/setup-pstack`，每个 role 都使用其默认 model。
- 项目中没有 `verify-*` skill 或其他 app harness，表示 agent 没有脚本化方式驱动应用。若问题涉及如何证明改动有效，提及 `/create-verification-skill`。
- 项目中没有 formal verification skill，表示没有超出测试所能覆盖范围的 property 检查。若问题涉及 race、invariant 或测试很少碰到的情形，提及 `/create-formal-verification-skill`。若已存在且其所覆盖的代码在该 skill 记录为「上次同步」的 commit 之后又有改动，且问题触及该代码，提及 `/maintain-formal-verification-skill`。

若缺少 model 规则且这很重要，询问用户是否现在要为每个 role 选择 model 与推理预算。这在新用户、问题关乎设置或成本、或答案取决于跑哪些 model 时很重要。每个 chat 最多问一次。若需求也不清楚，两个问题一起问。提供两种选择：

- 现在：让他们输入 `/setup-pstack`，并同时回答其问题。
- 稍后：先回答问题，并加一行说明：在未运行 `/setup-pstack` 之前，每个 role 保持默认 model。

## Get set up

1. 在聊天中用 `/add-plugin pstack` 安装，或在侧栏 Customize 中安装。
2. 运行 [`/setup-pstack`](../setup-pstack/SKILL.md)。它会询问推理预算、为每个 role 映射 model，并写入一条 rule。该 rule 作用于新 chat。随后会各提供一次：为项目生成 verification skill 与 formal verification skill（除非项目已有其一）。
3. 用 `/poteto-mode`、一个目标，以及能通过或失败的检查，开始真实任务。

安装本身不会改变任何事，直到用户调用某个 skill。只有 `/setup-pstack` 会从用户的原话加载。细节见 [README](../../README.md) 与 [guide page 1](../../docs/guide/01-setup.md)。可按 [`references/prompting.md`](references/prompting.md) 提议与用户一起措辞他们的第一条 prompt。

若担心成本，说明 token 花在哪里以及如何少花。pstack 会在 subagent 与 review panel 上多耗 token。重新运行 `/setup-pstack` 并选择更小的预算或更便宜的 model。将某 role 设为 `auto` 或 `inherit-parent` 时，会使用当前 chat 的 model；当 chat 运行在 Auto 或更便宜 model 上时，这能省 token。更短的 panel 列表会拉起更少 subagent，每个条目一个。把 `/poteto-mode` 留给需要严谨的工作。

pstack 面向 Cursor 构建，也可在 Claude Code 中通过 `/plugin marketplace add cursor/plugins`，再 `/plugin install pstack@cursor-plugins` 安装。在那里，每条命令带 `pstack:` 前缀，例如 `/pstack:poteto-mode`。[Claude Code adapter](../../docs/harness/claude-code.md) 映射 Cursor 名称；需要仅 Cursor 才有的能力（如 Bugbot 或 Automations）的步骤会跳过并附一行说明。

其他工具可以阅读这些 skill，因为它们使用 Agent Skills 格式。但多数工作流 skill（含 `/poteto-mode`、`/how`、`/why`、`/teach`）会拉起带 per-role model 的 Cursor subagent，且 Custom Modes 与 `/loop` 是 Cursor 特性，因此那些部分在其他环境中可能不可用。

## Start a task with `/poteto-mode`

`/poteto-mode` 将任务匹配到 playbook，把 playbook 的步骤复制进 todo 列表，并在步骤需要时运行其他 skill。跳过的步骤在列表中保留为 `skip: <reason>`。好的 prompt 说明目标以及如何判定完成。不要罗列 skill，因为手写顺序往往会丢掉或打乱 playbook 会保留的步骤。在帮助措辞前先读 [`references/prompting.md`](references/prompting.md)。示例见 [Guide page 2](../../docs/guide/02-poteto-mode.md)。

`/poteto-mode` 是否持续生效取决于用户如何启动：

- 在 `/poteto-mode` 上按 Enter 会把 skill 附在单条消息上；随 chat 推进会逐渐淡出。
- Mac 上 Option+Enter、Windows 上 Alt+Enter，或从 skill 条目选 Use as Mode，会设为 Custom Mode。在用户退出该 mode 之前，每轮都会留在上下文中，且不会混入随意闲聊。
- Cursor 文档在 Agents Window 与 CLI 中列出 Custom Modes。其他环境请在每个新任务开始时使用 `/poteto-mode`。

涉及此事时链接 [Cursor's skills docs](https://cursor.com/docs/skills)。chat 中途说「new task」会让 mode 匹配新的 playbook。`/poteto-mode` 已对其 playbook 步骤拉起的 subagent 使用 `poteto-agent`。若要从你自己的 subagent 得到同样风格，用 `subagent_type: "poteto-agent"` 拉起。

## Pick a skill

默认答案是 `/poteto-mode`，其步骤需要时会运行大多数其他 skill。当用户想要比 playbook 更多或更少某类能力时，直接点名 skill。推荐前先阅读该 skill，并给一个示例 prompt。

| 用户想要 | Skill |
|---|---|
| 以严谨方式完成任何非琐碎任务 | [`/poteto-mode`](../poteto-mode/SKILL.md) |
| 在开始前确认 agent 理解了他们的目标与问题 | [`/in-your-own-words`](../in-your-own-words/SKILL.md) |
| 在发送前把 prompt 写得更深而非更长：顶尖从业者会加什么、其 raw material 与方法、以及外部检查。返回 prompt，不执行任务 | [`/sharpen`](../sharpen/SKILL.md) |
| 了解代码现在如何工作，或新代码该放哪 | [`/how`](../how/SKILL.md) |
| 了解代码为何如此成形，或某个数字从哪来 | [`/why`](../why/SKILL.md) |
| 用平实语言理解某次改动或子系统 | [`/teach`](../teach/SKILL.md) |
| 补全自己在某主题上近期的工作上下文 | [`/recall`](../recall/SKILL.md) |
| 用平实文字与小图补全自用户上条消息以来本 chat 的进展，单独从回复即可列出并决定每项待用户处理的事 | [`/ketchup`](../ketchup/SKILL.md) |
| 像 `/ketchup` 一样补全，但以带字幕的幻灯片短视频呈现，待处理项以文字附在下方 | [`/ketchup-video`](../ketchup-video/SKILL.md) |
| 了解小 diff 可能在自身之外破坏什么 | [`/blast-radius`](../blast-radius/SKILL.md) |
| 在跨越函数边界的代码之前先落定类型与模块形状 | [`/architect`](../architect/SKILL.md) |
| 对同一 brief 多次尝试，合并为最佳结果 | [`/arena`](../arena/SKILL.md) |
| 以 cloud agent 对切片做并行检查，或 worker 竞速 | [`/swarm`](../swarm/SKILL.md) |
| 让不同 model 审 diff 并试图打破它 | [`/interrogate`](../interrogate/SKILL.md) |
| 存在廉价本地测试时先测后修 bug | [`/tdd`](../tdd/SKILL.md) |
| 对 `.ts` 或 `.tsx` 工作应用 TypeScript 规则 | [`/typescript-best-practices`](../typescript-best-practices/SKILL.md) |
| 审阅前去掉注释，并使用未撰写它们的 reviewer | [`/no-comments`](../no-comments/SKILL.md) |
| 把 AI 味从散文里清掉 | [`/unslop`](../unslop/SKILL.md) |
| 按标准写文档、RFC、README、PR 描述或 commit message | [`/technical-writing`](../technical-writing/SKILL.md) |
| 用平实语言再听一遍上条回复 | [`/bro`](../bro/SKILL.md) |
| 给 agent 脚本化方式驱动应用并证明行为 | [`/create-verification-skill`](../create-verification-skill/SKILL.md) |
| 让 verification skill 及其 feature map 与 app 重新对齐 | [`/maintain-verification-skill`](../maintain-verification-skill/SKILL.md) |
| 形式化验证测试很少触及的 race、invariant 或其他 property | [`/create-formal-verification-skill`](../create-formal-verification-skill/SKILL.md) |
| 在其所覆盖代码变更后让 formal verification 重新对齐，并弄清失败是 drift 还是 bug | [`/maintain-formal-verification-skill`](../maintain-formal-verification-skill/SKILL.md) |
| 向 PM 或 designer 展示用户所见状态图，并由 TLA+ model 校验，标出违反各规则的最短路径 | [`/statechart`](../statechart/SKILL.md) |
| 在报告或行动前审查性能数字 | [`/benchmark-checklist`](../benchmark-checklist/SKILL.md) |
| 跑大型或横切改动，或离开一段时间后再审的改动 | [`/figure-it-out`](../figure-it-out/SKILL.md) |
| 在一次运行中维护决策日志，并在结束后回顾 | [`/show-me-your-work`](../show-me-your-work/SKILL.md) |
| 为每个 role 选 model 与推理预算 | [`/setup-pstack`](../setup-pstack/SKILL.md) |
| 把用户自己的工作习惯变成 personal mode skill | [`/automate-me`](../automate-me/SKILL.md) |
| 把已完成任务的经验变成 skill 编辑 | [`/reflect`](../reflect/SKILL.md) |
| 阻止 agent 在本 repo 重复相同错误 | [`/correct`](../correct/SKILL.md) |
| 构建按钮通过 webhook 唤醒 Grok Bot 的页面 | [`/make-bot-ui`](../make-bot-ui/SKILL.md) |
| 在 pstack 里找路 | `/poteto-help` |

若与本 skill 相邻的某个 skill 目录未出现在表中，读其 frontmatter 并按 description 路由。`principle-*` 目录在下方 principles 部分覆盖。

易混情况：

- `/how` 解释代码做什么。`/why` 解释原因。`/teach` 运行其一或二者，并用平实语言解释结果。
- `/arena` 给每个 worker 相同 brief 并合并最佳部分。`/swarm` 把 work 切成切片或竞速并返回一份报告。
- `/architect` 落定设计后马上实现。加上「with checkpoint」可在写代码前审设计。
- `/interrogate` 审 diff。`/blast-radius` 寻找 diff 之外的破坏，并证明使改动安全的那一条事实。
- `/recall` 跨近期 chat 重建上下文。`/ketchup` 只覆盖自用户上条消息以来的本 chat。恢复某个具体 chat 或 branch 属于 Session pickup playbook。
- `/figure-it-out` 设计一次严谨运行。Orchestrate playbook 跑跨越多日与多 PR 的程序。Autonomous run playbook 把单任务驱动到完成条件。

不在 pstack 内：

- `/deslop`、`control-cli` 与 `control-ui` 随 `cursor-team-kit` plugin 分发。
- `/loop` 与 `/create-skill` 是 Cursor 内置。
- pstack 没有 `/orchestrate` skill。Orchestrate 是 `/poteto-mode` playbook。若 slash 菜单显示 `/orchestrate`，由其他 plugin 提供。

## Playbooks and principles

Playbook 是 `/poteto-mode` 内的步骤列表，不是 skill，因此没有 slash 命令。在 `/poteto-mode` 内描述任务会选中一个；下列说法可直接点名：

- 「babysit this pr」或「check on pr 123」运行 Babysit。它把 PR 推到 merge-ready 并在此停止。除非用户要求 merge、land 或 ship，否则不会 merge。
- 「land the stack」运行 Shipping。
- 「take over this branch」运行 Session pickup。
- 「pause safely」运行 Pause safely。
- 「full autopilot on this queue」运行 Autopilot-full。「autopilot, don't merge」运行 Autopilot-stack。
- 「run the eval playbook」运行 Eval。

没有 `/poteto-mode` 时，「babysit this pr」这类说法可能改为启动 Cursor 自带、做同一件事 skill。Playbook 列表见 [`poteto-mode`](../poteto-mode/SKILL.md) 的 Playbooks 一节。[Guide page 6](../../docs/guide/06-verify-and-ship.md) 涵盖开 PR、babysit 与 landing。

pstack 没有 planning skill。Cursor 的 Plan Mode 可与之并用。对跨阶段或 stacked PR 的工作，向 `/poteto-mode` 要 plan 会运行 [Multi-phase plan playbook](../poteto-mode/playbooks/multi-phase-plan.md)，它写 plan 不实现。设计类问题用 Prototype playbook 或 `/architect` 先在代码里落定。

Principle 是 `/poteto-mode` 会阅读并在回复中引用的单条规则 skill。用户很少直接调用。他们用名称来 steer，例如「apply prove it works. show me the real output.」输入 `/principle-<name>` 仍可按需加载。[Guide page 8](../../docs/guide/08-principles.md) 列出它们。

## Fix a run that went wrong

| 症状 | 处理 |
|---|---|
| 几轮之后 mode 不再生效 | 是用 Enter 启动的。以 Custom Mode 启动，或在每个任务开始时用 `/poteto-mode`。 |
| 一个问题被当成上一任务的下一步 | 说「new task」，或说明本轮不需要 mode。 |
| 新的 model 选择没有生效 | `/setup-pstack` 写入的 rule 作用于新 chat。开一个新 chat。 |
| 运行成本高于预期 | 见 Get set up 下的成本段。 |
| skill 没有自动加载 | 只有 `/setup-pstack` 从用户原话加载。其余在用户输入或 `/poteto-mode` 运行时才加载，且不会运行每个 skill。 |
| 并行 agent 互相覆盖 | 给每个 agent 独立 worktree，或作为 cloud agent 运行（各有一台机器）。 |
| 通宵运行有动静但没完成任何事 | `/loop` 需要能通过或失败的检查，而不是时长。见 [guide page 7](../../docs/guide/07-overnight.md)。 |
| 回复宣称绿构建即成功 | 要求真实命令、流程、存储值或 profile。那是 prove-it-works principle。 |

对 drift 的运行，[`references/prompting.md`](references/prompting.md) 有一行 steer。[Guide page 10](../../docs/guide/10-recipes-and-pitfalls.md) 有更多坑与值得复制的 recipe。

## Make pstack my own

- [`/automate-me`](../automate-me/SKILL.md) 根据用户自身历史草拟 personal mode skill，与 `/poteto-mode` 并用。
- 会话后 [`/reflect`](../reflect/SKILL.md) 把教训变成用户认可的 skill 编辑。
- `/poteto-mode write a skill for <workflow>` 运行 authoring playbook。eval playbook 盲测 skill 变更。
- 在独立 PR 中修行为异常的 skill，不要嵌在出错的功能工作里。

[Guide page 9](../../docs/guide/09-make-it-yours.md) 涵盖上述各项。

## Reply

先给答案。最多在一个代码块里给一个示例 prompt，若合适则从 [`references/recipes.md`](references/recipes.md) 改编，然后给出该文件链接。除非用户要整张地图，否则保持简短。
