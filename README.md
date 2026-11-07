# manfen-satck

从 pstack 提取的 agent skill 集合。每个 skill 是一个 `skills/<name>/SKILL.md` 文件，带 YAML frontmatter（`name`、`description`），并可附带按需加载的 references、scripts 和资源文件。

共 **58 个 skill**。

## Workflow & orchestration

| Skill | 作用 |
| --- | --- |
| [`architect`](skills/architect/SKILL.md) | 在写代码前先勾勒类型、签名与模块结构，并在实现填入时保持参与。用于 /architect、'architect this'、'design this'，或非平凡工作中直接写代码会锁死错误形态时。 |
| [`arena`](skills/arena/SKILL.md) | 对同一任务并行 spawn N 个候选方案，选定基线，再把失败方中最强的部分嫁接到基线上。用于 /arena、'arena this'、'throw it in the arena'，或当单次尝试非平凡产物会锁死错误形态时。 |
| [`figure-it-out`](skills/figure-it-out/SKILL.md) | 在更窄的 playbook 都不匹配时设计可审计 playbook：大型迁移、野心大的多段改动，或人离开后再审的工作。按任务缩放严谨度，跑假设循环并记录决策。用于 /figure-it-out 或「figure it out」。 |
| [`swarm`](skills/swarm/SKILL.md) | 拉起 N 个并行 worker，收齐结果后输出一份报告。用于 /swarm、「swarm this」，或并行覆盖、race、gauntlet 与探索。 |
| [`show-me-your-work`](skills/show-me-your-work/SKILL.md) | 为长时间或无人值守的工作保留可审查的决策轨迹：TSV 日志，每个决策一行（做了什么、为何、证据、结果）。默认仅本地；当审查者需要轨迹才能信任结果时再 commit。用于 /show-me-your-work、自主或多阶段运行，或用户离开后再来审查的工作。 |
| [`sharpen`](skills/sharpen/SKILL.md) | 让任务更深而不是更大，然后重写 prompt。用于 /sharpen。 |
| [`recall`](skills/recall/SKILL.md) | 从自有聊天记录、现场状态与共享记录（用户反馈、既往修复、事故）重建近期工作上下文，交回紧凑的当前状态摘要。用于 'recall my work on X'、'catch me up'、'what have I been working on'、'where did I leave off'，在开始或恢复工作前。 |
| [`reflect`](skills/reflect/SKILL.md) | 对当前 transcript 并行 spawn 三个 review subagent，提炼可沉淀经验，并把每条路由到既有 skill 上的具体编辑。用户说 reflect 时使用。 |
| [`correct`](skills/correct/SKILL.md) | 找出 agent 在本仓库反复犯的错，并让每类都不可能再犯。优先架构，其次类型，再是错误信息指明改法的 lint，然后测试，文档最后写。用真实历史错误证明每项检查会失败。操作者每次纠正你时重复此流程。用于 /correct。 |
| [`tdd`](skills/tdd/SKILL.md) | 仅在用户明确要求 TDD、失败测试或回归测试时使用，或当 bug 有明显且廉价的本地测试目标时使用。测试路径不清、成本高、偏集成或未请求时跳过。 |
| [`automate-me`](skills/automate-me/SKILL.md) | 用于「automate me」「创建/更新/刷新我的 -mode skill」「把偏好或工作方式固化成 skill」，或希望 agent 按用户方式工作。通过 create-skill + unslop 起草或修订个人 -mode skill，可选从近期 transcript 拉新证据。 |

## Review & verification

| Skill | 作用 |
| --- | --- |
| [`blast-radius`](skills/blast-radius/SKILL.md) | 在交付前找出变更可能在别处搞坏什么——超出 diff 范围——并通过跑真实代码而非写说明来证明它安全所依赖的那一条事实。用于「X 的 blast radius」「这可能搞坏什么」，或不信任的小 diff 审查。 |
| [`interrogate`](skills/interrogate/SKILL.md) | 用于「interrogate」「对抗式 review」「多模型 review」「challenge this」「stress test this code」「找盲点」或「tear this apart」。对手试图击穿变更或设计，仅在一次 run 能证明的发现保留。 |
| [`benchmark-checklist`](skills/benchmark-checklist/SKILL.md) | 在汇报或据此行动前，审查性能测量（limiter、调优、上限、错误、可重复性、相关性、工作是否真的发生）。在跑 benchmark 或汇报测得的加速/回归时使用。 |
| [`create-verification-skill`](skills/create-verification-skill/SKILL.md) | 生成项目本地的 verification skill，像用户一样驱动应用——任意语言、框架或平台。用于 /create-verification-skill、「给这个仓库做控制 skill」，或项目没有脚本化方式证明 UI/CLI/服务行为时。 |
| [`maintain-verification-skill`](skills/maintain-verification-skill/SKILL.md) | 周期性维护：让项目的 verification skill 与 feature map 保持可信——按 feature 并行只读读源码、一次 live 会话驱动全部 feature，最多一个 PR 承载已证实的修正。用于 /maintain-verification-skill 或「审计 verify skill」。 |
| [`create-formal-verification-skill`](skills/create-formal-verification-skill/SKILL.md) | 生成项目本地 skill，在 bug 代价最高、测试最难触及之处，用合适验证工具对任意语言的代码做形式化验证。用于 /create-formal-verification-skill、「model-check this race」、「prove this invariant」，或当并发、重试、状态机、解析器、unsafe 代码需要超出测试的保障时。 |
| [`maintain-formal-verification-skill`](skills/maintain-formal-verification-skill/SKILL.md) | 在代码变更后把项目的形式化验证与代码重新对齐：找出改动过已验证代码的 commit，更新 model、harness 或 property 以描述当前代码，重跑验证，并说明每次失败是验证漂移还是代码 bug。用于 /maintain-formal-verification-skill、「model 是否仍忠实于代码」，或在合并与重构触及已验证代码却未复查时使用。 |
| [`statechart`](skills/statechart/SKILL.md) | 对会在状态间迁移的系统（如产品流程、协议或服务）做 TLA+ model 检查，并绘制成 Mermaid statechart。报告给出到达各违反规则状态的最短路径。用于 /statechart，或把 bug 展示为穿越状态的路径。 |
| [`no-comments`](skills/no-comments/SKILL.md) | 拉起 Comment Sicko，处理已接受的发现，并对声称的约束提供编码方案。 |

## Understanding & explanation

| Skill | 作用 |
| --- | --- |
| [`how`](skills/how/SKILL.md) | 用于「X 怎么工作」、改代码前的走读，以及归属 / 分层问题（「该放哪」「哪个包管这个」「这是不是正确的层」）。解释子系统架构、运行时流程、上手心智模型。动机类问题用 why。 |
| [`why`](skills/why/SKILL.md) | 用于「为什么 X 是这样实现的」「为什么选 Y」、设计理由、回归、事后复盘或数据支撑的阈值。发现可用 MCP，并行查询各证据类别（源码管理、issue tracker、长文档、即时聊天、基础设施可观测性、错误追踪、产品分析数仓），再返回带引用的决策与权衡说明。运行时行为用 how。 |
| [`teach`](skills/teach/SKILL.md) | 用平实语言解释一段工作，让人真正理解。运行 `how` 与 `why` skill，将其发现编织成一份清晰说明。用于「教我懂这个」「帮我真正理解 X」「向我解释这次变更或子系统」。 |
| [`bro`](skills/bro/SKILL.md) | 用大白话复述上一条消息，不要术语。 |
| [`in-your-own-words`](skills/in-your-own-words/SKILL.md) | 用自己的话复述你认为用户的目标以及他们在解决的问题。用于 /in-your-own-words。 |
| [`ketchup`](skills/ketchup/SKILL.md) | 用平实语言和少量图示，帮用户弄清自其上条消息以来本聊天里发生了什么变化。列出每件等他们拍板的事项，仅凭回复即可做决定。用于 /ketchup。 |
| [`ketchup-video`](skills/ketchup-video/SKILL.md) | 像 /ketchup 一样帮用户赶上进度，用带字幕的短幻灯片视频展示发生了什么，等待的 call 以文字放在视频下方。用于 /ketchup-video。 |

## Writing & style

| Skill | 作用 |
| --- | --- |
| [`unslop`](skills/unslop/SKILL.md) | 从任意文稿中剔除 AI 写作痕迹。 |
| [`technical-writing`](skills/technical-writing/SKILL.md) | 分层技术写作标准：Diátaxis 结构、Google 开发者风格句式、STE 指令规则、Global English 语法。用于 /technical-writing，或在撰写或审阅文档、RFC、readme、PR 描述、commit 消息时使用。 |
| [`poteto-mode`](skills/poteto-mode/SKILL.md) | poteto 的 agent 风格：回复简洁细致、审慎使用 subagent、散文去 slop、代码从简、工作可验证。用于 poteto、/poteto-mode，或要求以本风格工作时。 |
| [`poteto-help`](skills/poteto-help/SKILL.md) | 引导用户完成 pstack 设置、/poteto-mode 使用，以及为任务挑选 skill、playbook 或 principle。在聊天里输入 /poteto-help 并带上问题。 |

## Setup & language

| Skill | 作用 |
| --- | --- |
| [`setup-pstack`](skills/setup-pstack/SKILL.md) | 配置 pstack 各角色使用的模型及推理预算。检测你可用的模型，并写入一条始终应用的规则以覆盖 skill 默认值。用于 /setup-pstack、「configure pstack models」、「pstack budget」，或更改 pstack 的模型选择。 |
| [`typescript-best-practices`](skills/typescript-best-practices/SKILL.md) | TypeScript 最佳实践。在阅读或编辑任何 .ts 或 .tsx 文件时使用。 |
| [`make-bot-ui`](skills/make-bot-ui/SKILL.md) | 在构建应通过 webhook 唤醒 Grok Bot 的自定义 UI（页面、仪表盘、按钮）时使用；当用户须提供 webhook sender key 时；或将该 UI 暴露在 Tailscale 上时使用。 |

## Principles

小而常驻的原则：每条都写明触发它的情境和对应的动作。

| Skill | 作用 |
| --- | --- |
| [`principle-attack-the-premise`](skills/principle-attack-the-premise/SKILL.md) | 当两个及以上共享同一前提的修复在同一关卡失败时适用。写下前提并直接检验，再写下一个仍假设它的修复。 |
| [`principle-boundary-discipline`](skills/principle-boundary-discipline/SKILL.md) | 在接 validation、错误处理或框架 adapter 时适用。把 guard 集中在系统边界（CLI、config、网络、外部 API）；内部信任类型，业务逻辑放在纯函数里。 |
| [`principle-build-the-lever`](skills/principle-build-the-lever/SKILL.md) | 适用于任何非琐碎工作，不限于批量：编辑、迁移、分析、检查。造工具来执行或证明（codemod、script、generator，或 subagent 遵循的 skill），而非手做。工具是 reviewer 可重跑的产物。 |
| [`principle-encode-lessons-in-structure`](skills/principle-encode-lessons-in-structure/SKILL.md) | 当你发现自己在写第二遍相同指令，或注意到反复出现的纠正时适用。把规则编码为 lint、metadata flag、runtime 检查或 script，而不是再加文字。 |
| [`principle-exhaust-the-design-space`](skills/principle-exhaust-the-design-space/SKILL.md) | 面对代码库无先例的新 UI 交互或架构决策时适用。先造 2–3 个竞争原型并排对比，再 commit。 |
| [`principle-experience-first`](skills/principle-experience-first/SKILL.md) | 产品、UX 或功能范围权衡出现时适用。用户愉悦优先于实现便利；少而精胜过多而糙。 |
| [`principle-explain-the-number`](skills/principle-explain-the-number/SKILL.md) | 在度量之前，以及信任、汇报或依据某数字行动之前适用：加速比、回归、吞吐、延迟或 eval 结果。找出限制因素，并排除测到的并非你以为的工作。 |
| [`principle-fix-root-causes`](skills/principle-fix-root-causes/SKILL.md) | debug 时适用。把每个症状追溯到系统首次出错之处并在那里修。guard 仅在所处理状态合法时才算修复。 |
| [`principle-foundational-thinking`](skills/principle-foundational-thinking/SKILL.md) | 写逻辑之前适用：选核心类型与数据结构、编排 scaffold 与 feature 顺序、问并发 actor 共享什么。先把数据结构做对，下游代码自然会清晰。 |
| [`principle-guard-the-context-window`](skills/principle-guard-the-context-window/SKILL.md) | context 将满时适用：大输出、长文件、重复读取、fan-out 规划。大块交给 subagent；主线程留摘要，不要原始 payload。 |
| [`principle-laziness-protocol`](skills/principle-laziness-protocol/SKILL.md) | 重构、评估 diff 大小，或想加抽象、层、信号穿线时适用。偏向删除与解决问题的最小改动。 |
| [`principle-make-operations-idempotent`](skills/principle-make-operations-idempotent/SKILL.md) | 设计在 crash、重启与重试中运行的命令、生命周期步骤或处理循环时适用。无论先前 run 是否部分完成，都收敛到同一终态。 |
| [`principle-migrate-callers-then-delete-legacy-apis`](skills/principle-migrate-callers-then-delete-legacy-apis/SKILL.md) | 在引入新内部 API 而旧调用方仍存在时采用。在同一波迁移中迁完调用方并删除旧 API，而不是保留兼容层。 |
| [`principle-minimize-reader-load`](skills/principle-minimize-reader-load/SKILL.md) | 在审阅或整理难以追迹的代码时采用。统计从问题到答案之间的层数，以及读者脑中须维持的隐藏状态；折叠仅单处调用的 wrapper，缩小可变作用域。 |
| [`principle-model-the-domain`](skills/principle-model-the-domain/SKILL.md) | 在编写有状态逻辑，或代码分支很多、在多个文件中重复同一形状假设时采用。 |
| [`principle-never-block-on-the-human`](skills/principle-never-block-on-the-human/SKILL.md) | 在可逆工作上想要问「我该做 X 吗？」时采用。 |
| [`principle-outcome-oriented-execution`](skills/principle-outcome-oriented-execution/SKILL.md) | 在有明确阶段边界的计划内重写与迁移过程中采用。收敛到目标架构；不要用一次性兼容代码维持顺滑的中间态。 |
| [`principle-prove-it-works`](skills/principle-prove-it-works/SKILL.md) | 在完成任务之后、声明完成之前采用。对照真实产物验证（跑功能、读实际值、看 diff），而非代理指标、自述或「能编译」。 |
| [`principle-redesign-from-first-principles`](skills/principle-redesign-from-first-principles/SKILL.md) | 把新需求并入既有设计时采用。 |
| [`principle-separate-before-serializing-shared-state`](skills/principle-separate-before-serializing-shared-state/SKILL.md) | 当并发 actor 可能写入同一文件、分支、键或状态对象时采用。先消除共享；仅当单一共享写入方是真实不变量时，才在结构上串行化。 |
| [`principle-sequence-verifiable-units`](skills/principle-sequence-verifiable-units/SKILL.md) | 用于多步工作（sweep、迁移、同类编辑批量执行）以及 commit、PR 的叠放方式。把工作拆成以可验证状态收尾的小单元，每步先绿再进下一步，并按能让 reviewer 自行重放的顺序交付。 |
| [`principle-subtract-before-you-add`](skills/principle-subtract-before-you-add/SKILL.md) | 在排期新增、重构或重写时采用。先移除死代码、冗余 validator 与 stub 引用，再在更简基座上构建。 |
| [`principle-test-behavior-not-implementation`](skills/principle-test-behavior-not-implementation/SKILL.md) | 在编写、修改或保留测试时采用。断言代码用户可观察到的行为，使行为坏时测试失败、仅内部改动时仍通过。 |
| [`principle-type-system-discipline`](skills/principle-type-system-discipline/SKILL.md) | 在设计类型、审阅函数签名或在任何静态类型语言中写代码时采用。让非法状态无法表示、为语义原语加 brand、在边界解析外部数据、穷尽 variant、从权威 schema 推导。 |
