---
name: principle-prove-it-works
description: "在完成任务之后、声明完成之前采用。对照真实产物验证（跑功能、读实际值、看 diff），而非代理指标、自述或「能编译」。"
disable-model-invocation: true
---

# Prove It Works

通过直接检查真实事物来验证每项任务产出。不要从代理指标、自述或「能编译」推断。

**Why:** 未验证的工作其正确性未知。间接验证（文件 mtime、输出新鲜度、agent 自述、缓存截图）感觉比直接观察便宜，但按错误推断行动的成本远高于查源码。

查真实事物，别查代理：
- 直接检查进程是否存活，不要通过派生状态间接推断
- 读实际值，不要读缓存或派生表示
- 验证失败时，先怀疑观察方法，再怀疑系统

## Script the check when you can

最强证明是可重复跑同一套比较的确定性脚本，而不是一次性肉眼扫一眼。写好脚本、运行它，并保留输出作为 reviewer 可重跑的产物，而不是只听你口头保证。

让人类能看见该产物。只有在大体量或复杂工作中，事后审计链路才需要 commit（例如大型 port 或 migration，见 **show-me-your-work** skill）。
