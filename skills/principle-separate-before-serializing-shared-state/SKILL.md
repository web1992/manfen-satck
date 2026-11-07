---
name: principle-separate-before-serializing-shared-state
description: "当并发 actor 可能写入同一文件、分支、键或状态对象时采用。先消除共享；仅当单一共享写入方是真实不变量时，才在结构上串行化。"
disable-model-invocation: true
---

# Separate Before Serializing Shared State

当并发 actor 可能共享可变状态时，先问他们是否真需要同一个可变对象。若不需要，消除共享。共享不可避免时，在结构上强制串行：lockfile、顺序阶段、独占所有权。说明与约定不是并发控制。

**Why:** 对共享状态的并发写入会产生间歇、难复现、调试昂贵的 race condition。

**Pattern:**
1. **识别共享可变状态**（双方都读写的文件、都 push 的分支、既定义又消费的 API 等）。
2. **默认：消除共享写入目标。** 问：这些 actor 需要一个规范对象，还是在发布各自独立的事实？给每个 actor 自己拥有的文件、键、分支或状态目录，仅在读/汇报边界合并。两个 worker 把各自的 `lastX` 字段写进同一个 `state.json` 仍是共享 mutation。`indexer-state.json` + `metrics-state.json` 则不是。
3. **仅当单一共享写入目标是真实不变量时，在结构上串行访问**（lockfile、顺序阶段、单写入 actor，或原子 compare-and-swap）。把「我们需要 lock」当作待核对的设计气味，而非默认答案。
