---
name: figure-it-out
description: "在更窄的 playbook 都不匹配时设计可审计 playbook：大型迁移、野心大的多段改动，或人离开后再审的工作。按任务缩放严谨度，跑假设循环并记录决策。用于 /figure-it-out 或「figure it out」。"
disable-model-invocation: true
---

# Figure it out

任务对不上任何既有 playbook 时，设计一个。任何代码之前要先交付的是工作流本身：一串阶段，按任务缩放严谨度、跑科学方法，并留下人离开后可审计的决策轨迹。

## Start

打开 todolist，第一项是阅读 **poteto-mode** skill 的 Principles 一节。再把下面各阶段加为 todo。

## Phase A: Frame

先 grounding，再 commit。在能说清以下事项前不要开跑：

- 完成定义是可证伪谓词（**prove-it-works** principle skill）。
- 范围量化：粗估单位与工作量，加上 grounding 浮出水面的 blocker。
- 严谨等级，偏高估。单向门与高 blast radius 要更多；可逆、低 stakes 步骤可更少。严谨是 gate 与 artifact，不是「更努力试试」。

在长跑 commit 前先呈现 framing 与权衡。可逆工作可继续（**never-block-on-the-human** principle skill），但数小时级的 run 值得一个 checkpoint。

## Phase B: Design the workflow

拆成原子、可独立落地的单元。按风险/未知优先排序。脚手架与验证在 feature 之前（**foundational-thinking** principle skill）。

- 在工作之前建好 verification harness，从变更前状态捕获 baseline，使检查读成「旧值 vs 新值」。
- 单向门设计决策跑 **architect** skill（它跑 **arena**）。形状已具体的机械工作跳过。对已落定设计再跑一轮 arena 是过度工程（**laziness-protocol** principle skill）。
- 决定什么可扇出。仅在接缝处并行，每个 worker 自有 worktree 或 branch（**separate-before-serializing-shared-state** principle skill）。别过度扇出。按 **poteto-mode** skill 的 Subagents 一节为每个 subagent 选推理等级与 fast/standard 速度。
- 把设计好的阶段列表写下来。人审的是这份列表。

然后执行设计。把其步骤作为具体项加进 todolist，插在 Phase C 与 Phase D 条目之间。每项在 Phase C 循环纪律下跑，并穿插 Phase D 日志——每步落地记一行，而非把整条轨迹留到最后。

## Phase C: Run the loop

每个单元是一次实验。陈述假设、做最小改动、在真实 artifact 上对照谓词度量，推进则保留，否则回滚。应用 **sequence-verifiable-units** principle skill：下一步开始前验证当前单元，而非最后批量检查。

- 通过检查 artifact 验证，不信自报。若某关过得太轻松，先怀疑观察方法再怀疑系统。
- 委派工作与 judge 配对。worker 钻 gate 空子则重置并收紧契约。gate 本身错了则在独立改动里修 gate，不要绕路。
- 裁决是 VERIFIED、NOT VERIFIED 或 INCONCLUSIVE。INCONCLUSIVE 不是 pass。别掩盖负面。

## Phase D: Keep the audit trail

通过 **show-me-your-work** skill 记录 run。figure-it-out 的工作通常足够野心，应 commit 轨迹以便审阅者在 PR 中阅读。轨迹加 diff 让人回来能信任这项工作。

## Phase E: Verify and hand back

在真实产品上对照 Phase A 谓词检查整体，而不只是 harness。把反复出现的纠正编码为 gate、lint 规则、检查或脚本（**encode-lessons-in-structure** principle skill）。

**Reply：** 你设计的 playbook、严谨等级及理由、决策轨迹路径、相对谓词已验证项、仍开放项。
