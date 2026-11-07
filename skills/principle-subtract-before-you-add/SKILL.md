---
name: principle-subtract-before-you-add
description: "在排期新增、重构或重写时采用。先移除死代码、冗余 validator 与 stub 引用，再在更简基座上构建。"
disable-model-invocation: true
---

# Subtract Before You Add

演化系统时，先减复杂度，再构建。默认做减法。

把简化当作持续投资。离开时让设计在相同或更小的表面上，比你接手时略更简单、略更有能力。

- 先移除再建设
- 先砍再抛光（在投入质量之前先到最小形态）
- 按观察到的用法设计，而非投机的 edge case
- 不要超出 spec 要求的投机 validator、parser 或 guard
- 简化 prompt（去掉冗余说明、过多模板）
- 引用若无新内容，删掉而非留 stub
