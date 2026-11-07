---
name: principle-foundational-thinking
description: "写逻辑之前适用：选核心类型与数据结构、编排 scaffold 与 feature 顺序、问并发 actor 共享什么。先把数据结构做对，下游代码自然会清晰。"
disable-model-invocation: true
---

# Foundational Thinking

**结构决策**保护选项价值。**代码级决策**保护简洁。

**数据结构优先。** 写逻辑前先定对数据形状。尽早定义核心类型，追踪每种访问模式，选结构匹配主路径。

代码级上，DRY 结构，不 DRY 每一行。类型与数据模型应收敛。三句相似仍好过过早抽象。显式优于取巧。测行为与边界情况，不测行数。

**并发推论。** 在 actor 间共享状态前，问「若另一 actor 并发改这里会怎样？」若答案不是「nothing」，就隔离。

**Scaffold 优先。** 若某物惠及之后每个阶段，先做它。问「后续每个阶段是否都因它已存在而受益？」CI、lint、测试基础设施与共享类型是 scaffold。按选项价值排序：先 setup 后 feature，先测试后修复。commit 小且单一目的。

每个增量应落地一个连贯抽象，或加深已有抽象。不要把新能力摊成各调用方的特例协调。

减法在 scaffold 之前。先删死代码，再铺基础。
