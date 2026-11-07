---
name: architect
description: "在写代码前先勾勒类型、签名与模块结构，并在实现填入时保持参与。用于 /architect、'architect this'、'design this'，或非平凡工作中直接写代码会锁死错误形态时。"
disable-model-invocation: true
---

# Architect

用 `not implemented` 体与伪代码勾勒类型、函数签名、类形态与模块边界。综合多模型视角后，按选定草图填入代码。

## Start

开始前打开 todolist，每个阶段一条。

1. Ground
2. Sketch
3. Agree
4. Implement
5. Scrap

## Phase A: Ground the problem

对新代码触达的每个系统建立真实心智模型。对相关子系统运行 **how** skill。点名文件不是 grounding。产出 **how** 所规定的架构说明。若设计重定义归属或分层，还要对现有形态运行 **why** skill，使理由成为约束而非猜测。

仅当工作确属无周边系统可集成的真 greenfield 时，可跳过 Phase A。

## Phase B: Sketch

以设计草图任务和 Phase A grounding artifact 运行 **arena** skill。将 `references/runner-prompt.md` 作为每个 runner 的 prompt。

从 `pstack-models.mdc` 规则的 `architect runners` 行取 runners，替代 `arena runners` 行。若规则或该行缺失，用 `claude-opus-5-5-xhigh` 与 `grok-4.7-xhigh-fast`。别名与被拒绝的条目遵循 **arena** skill Phase A 的 runner 规则。

设计做两遍。综合前至少要求两个结构不同的候选，即便第一个看起来已够。这是 **exhaust-the-design-space** principle skill 的具体化。整形态备选，而非单一形态内的点修。

综合前用 [`references/design-red-flags.md`](references/design-red-flags.md) 筛每个候选。假定下一贡献者是只看到自己打开文件的 agent：抄最近示例、走最短可编译路径。优先选「从单文件看对的改动对整个 repo 也对」的设计。

在可行候选间比较 interface 深度。优先把更多复杂度藏在更小、更简单的公开面背后的设计。丰富的 interface 可通过集中能力而非散层保持调用链短。

Arena 返回一个综合设计包；其 synthesis 决定填入 rationale 的「Synthesis decision」段。

## Phase C: Agree (opt-in)

默认：直接用综合设计进入实现，无人工检查点。

调用方明确要求时选择启用检查点：如「/architect with checkpoint」「stop and show me before implementing」等。此时展示综合设计并暂停待签核。

无论哪种方式，synthesis 都可作为独立 commit 交付，即 **foundational-thinking** principle skill 的「scaffold first」模式。填充过程中的计划内、有范围的破坏可以接受，见 **outcome-oriented-execution** principle skill。实现前对设计施加对抗压力，可对综合草图运行 **interrogate** skill。

若人类对形态有异议（检查点中或事后），视为 Phase A 证据；重新 ground 并重跑 Phase B，再写更多代码。

## Phase D: Implement against the sketch

用代码替换 `not implemented` 体，用逻辑替换伪代码。综合草图是契约。偏离它是需要显式摆出的信号，不是应默默吸收的摩擦。若函数需要草图未预料的参数，要判断是草图错了、需求漏了，还是实现过度。

## Phase E: Scrap when the architecture is wrong

若实现不断产生草图无法吸收的摩擦，扔掉草图。不要往错误设计上打补丁，见 **redesign-from-first-principles** 与 **fix-root-causes** principle skills。

信号是*模式*，不是单例。迹象包括：

- 同一形状的 workaround 在无关代码中反复出现。
- 多个无关 edge case 都需要特殊分支。
- 类型需要逃生舱（`any`、cast、实践中恒被赋值的 optional 字段）才能编译。
- 草图说状态不共享，却出现「需要 lock」 reflex。
- 调用方必须了解抽象内部规则才能使用。
- 实现中出现两条及以上独立、同形的 Phase D 偏离。

用判断。少量 edge case 不判死刑；有些问题确实复杂。数据中的复杂不等于设计中的复杂。

scrap 时：

1. 对已构建部分重跑 **how** skill。
2. 按 redesign-from-first-principles，把新约束当作 day-one 假设来重设计。
3. 按 **subtract-before-you-add** principle skill 先减后增；新草图在长大前应比旧的更小。
4. 回到 Phase B 重跑 arena。

## Outputs

先写调用方用法，再衍生类型草图。小改动：单文件新类型与签名。大工作：模块图加类型定义。rationale 一并交付，按 `references/rationale-template.md` 成形，含用法草图与 synthesis 决定。
