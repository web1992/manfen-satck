---
name: principle-explain-the-number
description: "在度量之前，以及信任、汇报或依据某数字行动之前适用：加速比、回归、吞吐、延迟或 eval 结果。找出限制因素，并排除测到的并非你以为的工作。"
disable-model-invocation: true
---

# Explain the Number

- **跑之前写好决策。** 点名会改变决策的结果，以及噪声底。run 间离散度大于效应时停止。
- **问「为什么不能翻倍？」** 点明限制结果的资源或代码路径，如核心、锁、磁盘、网络或负载生成器本身。用 profile 或 run 期间的系统计数得到，再映射到源码。靠读代码猜不算 limiter。说不清为何数字不能好一倍，就不知测的是什么。
- **列出数字还可能测到什么，并用证据逐项排除。** 常见嫌疑：错误、跳过或缓存的工作、从未运行的代码、未调的一侧（仍用默认设置）、噪声、以及端到端无足轻重的片段。
- **把证据与数字一起保留。** 在备注或链接产物里写 run 次数、离散度与 limiter。

性能数字按 [benchmark-checklist](../benchmark-checklist/SKILL.md) skill 跑完整流程。对 eval 结果，对 trial 问同样问题：每次 run 是否做了任务、差距跨 trial 与 model 是否成立、场景是否重要。

以下情况视为跳过本原则：run 开始时没有会改变决策的结果；数字背后证据无 run 次数、无离散度或无命名 limiter；或节省的时间大于改动片段所花时间。

区别于 [Prove It Works](../principle-prove-it-works/SKILL.md)——后者检查输出是否真实。
