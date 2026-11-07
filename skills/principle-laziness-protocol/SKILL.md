---
name: principle-laziness-protocol
description: "重构、评估 diff 大小，或想加抽象、层、信号穿线时适用。偏向删除与解决问题的最小改动。"
disable-model-invocation: true
---

# Laziness Protocol

- **合并决策。** 不要在多处重复同一选择。放在单一真相源后，把结果当作简单 flag 传递。
- **最小化 diff。** 做解决问题的最小改动。更少行胜过「优雅」样板。
