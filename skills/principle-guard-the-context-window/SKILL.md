---
name: principle-guard-the-context-window
description: "context 将满时适用：大输出、长文件、重复读取、fan-out 规划。大块交给 subagent；主线程留摘要，不要原始 payload。"
disable-model-invocation: true
---

# Guard the Context Window

**原因：** context 溢出会降低推理质量、产生压缩伪影并阻断进展。

**模式：**
- **分阶段并 cap scope。** 限制每阶段文件数、设 turn 预算、计入机制成本。
