---
name: benchmark-checklist
description: "在汇报或据此行动前，审查性能测量（limiter、调优、上限、错误、可重复性、相关性、工作是否真的发生）。在跑 benchmark 或汇报测得的加速/回归时使用。"
disable-model-invocation: true
---

# Benchmark checklist

在产出性能数字时使用：PR 前后对比、回归声称、hillclimb harness、库或配置选择。每题用一次运行的证据回答，不要靠猜代码。

用户要的快速 ballpark，单次运行即可；仍须检查第 4、7 题，并说明仅一次运行。除非该次看起来不对，其余可跳过。在选项之间做选择从来不是 ballpark。

## Before you run anything

- 写下你预期要作出的声称，用你会 ship 的措辞（如「在 60k 行数据集上 export 的 p50 快 30%」）。下面各题检验这句话。
- 读测量脚本。记下计什么时、计什么、忽略什么。
- 用 `uptime` 看 load average，用 `nproc` 看核心数。机器忙时查清在跑什么。若不能停掉，交错跑两侧使噪声相同，并在报告中说明。

## The questions

1. **Why not double?** 点名 limiter。在不纳入报告的跑次里 profile，因为 profiler 与 tracer 会拖慢工作。用每进程 CPU（`top`、`pidstat`）、运行时 profiler（`node --cpu-prof`、`py-spy`、`perf`）、I/O wait、系统调用计数（Linux 上 `strace -c`），再把热点映射到源码。也看负载生成器：若它先饱和，你测的是负载生成器。若改动没动数字，limiter 解释原因，先找到它再判改动无用。
2. **Was it tuned?** 每侧按 prod 方式跑：release 构建、prod 标志与环境、批处理与事务设置、连接池、缓存冷热与 prod 一致、相同版本与数据。一侧用默认即在比配置而非实现。limiter 若是某项设置（每行一 commit、debug 构建、缺索引），该侧未调优。调优后再测再选赢家。无法调优则不要从该次运行选赢家。用户在选择采纳物时，把声称收窄到「今日 ship 的代码」不能修这个，因为他们采纳的是选项，不是今日设置。
3. **Did it break limits?** 做算术。字节每秒与磁盘、网络带宽比；每秒操作数 × 每操作成本与核心数比；省下的时间与被改片段耗时比。去掉占运行 10% 的片段，最多大约快 11%。超过物理上限的结果测的是别的东西：缓存、no-op 或 bug。
4. **Did it error?** 统计失败与非成功响应，并核对输出正确而不只是存在。拒绝往往快，超时与重试慢。脚本不统计错误则加上计数。
5. **Does it reproduce?** 每侧至少跑 5 次，交错（A、B、A、B……）使 warmup、懒初始化、缓存与漂移不偏袒任一侧。报中位数与区间。差距小于 run-to-run 波动则无可测差异。接近时用 rank-sum test 或 harness 自带统计。
6. **Does it matter?** 任何微观结果旁，用真实数据规模与并发测用户等待的端到端路径，并把微观结果报成整体的占比。占请求 1% 的 helper 最多让请求快 1%，helper 再快亦然。
7. **Did it even happen?** 确认工作在计时区内发生：请求到达服务器、行已写入、字节已读、代码使用了结果。懒代码（没人迭代的 generator、没人 await 的 promise、JIT 可丢弃的结果）与超时会产出「工作从未发生」的数字。

## Report

- 先给裁决：更快、更慢、无可测差异、或 inconclusive。
- 给出数字及单位、运行次数、区间、limiter。例如「p50 41 ms → 33 ms，每侧 7 次中位数，之后区间 32–35 ms，单核 JSON 解析 bound。」
- 声称有差异却说不清 limiter、一侧未调优、或无法检查第 4、7 题时，判 inconclusive。点名差距。
- PR body 只保留一个主数字，见 **Opening a PR** playbook。运行、区间、limiter 证据放在链接 artifact 或 notes 文件。

## Other perf material

- 在 **Perf issue** playbook 据此规划前，先审查其 baseline；之后每个数字同样审查。
- 在 **Hillclimb** playbook 的 harness 冻结前审查。冻结后 harness 打印 error 与 work 计数，每次 keep-or-revert 免费覆盖第 4、7 题。
