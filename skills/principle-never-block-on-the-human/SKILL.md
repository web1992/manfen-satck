---
name: principle-never-block-on-the-human
description: "在可逆工作上想要问「我该做 X 吗？」时采用。"
disable-model-invocation: true
---

# Never Block on the Human

人类异步监督。Agent 必须保持不阻塞。做合理决定、继续推进，让人类事后纠偏。代码变更可逆且可 review，错误决定通常比阻塞更便宜。

- 先做完，再呈现。干活、展示结果。别问「我该做 X 吗？」——做 X，并说明理由。
- 让系统具备自愈合能力。发现问题时记录并在下一轮修复。
- 不可逆操作（force-push、删除生产数据、发送外部消息）仍须确认。
- 可逆操作（写代码、改笔记、拆分任务）应在不阻塞的情况下推进。
- 产品方向来自人类。执行不应阻塞。
