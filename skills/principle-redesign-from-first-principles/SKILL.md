---
name: principle-redesign-from-first-principles
description: "把新需求并入既有设计时采用。"
disable-model-invocation: true
---

# Redesign From First Principles

集成变更时，不要把它硬焊在现有设计上。按「若该需求从一开始就是 foundational 假设，我们会怎么写」来重新设计。

- 读清所有受影响文件，理解当前设计
- 自问：「若从零开始写，且带着这项新需求，我们会建什么？」
- 把变更传播到每一处引用：类型、文档、示例、rationale 章节
- 先想清楚整体设计，再分步交付

这是在既有设计中集成变更时保留 option value 的做法。
