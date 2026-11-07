---
name: principle-attack-the-premise
description: "当两个及以上共享同一前提的修复在同一关卡失败时适用。写下前提并直接检验，再写下一个仍假设它的修复。"
disable-model-invocation: true
---

# Attack the Premise

当两个及以上共享同一前提的修复在同一关卡失败时，怀疑前提，而不是修复。

**原因：** 每次失败的修复都是对其所假设信念的反证。第三次在同一前提上修，是把力气花在证据已表明原因不在那里的地方。

**模式：**
- **写下前提。** 前提是每个失败修复都假设的那一句话。
- **在下一修复之前检验前提。** 找一项若前提为假则结果不同的观察，并做出该观察。看失败如何分布，而不只看规模。按 [Build the Lever](../principle-build-the-lever/SKILL.md) 让检查可重跑。
- **跟证据走。** 若与前提矛盾，原因在别处。找失败共同点里前提未预测的部分。若确认前提，按 [Fix Root Causes](../principle-fix-root-causes/SKILL.md) 追问下一个「why」。更早的修复可能治的是后果而非原因。
- **按 [Laziness Protocol](../principle-laziness-protocol/SKILL.md) 去掉条件而非补偿。** 若证据显示某部分总在输的一边，改是什么把它放到那里。在每次 run 后清理的机制会保留原因并增加工作量。

**停止：** 前提未写下并检验之前，不要开始下一项修复。

本原则区别于 [Redesign from First Principles](../principle-redesign-from-first-principles/SKILL.md)——后者围绕新需求重建设计。本原则质疑当前设计所假设的事实。
