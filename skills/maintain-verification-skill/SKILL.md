---
name: maintain-verification-skill
description: "周期性维护：让项目的 verification skill 与 feature map 保持可信——按 feature 并行只读读源码、一次 live 会话驱动全部 feature，最多一个 PR 承载已证实的修正。用于 /maintain-verification-skill 或「审计 verify skill」。"
disable-model-invocation: true
---

# Maintain a verification skill

应用一变，feature map 就开始腐化。本 skill 是 `/create-verification-skill`（或任何带 feature map 的项目本地 verification skill）所生成 skill 的保养循环。严谨的单位是 feature，而不是逐句：从源码覆盖每个 feature 文件，并在 live 中演练每个 feature，不必把每条 bullet 都 terminal 化。

## Outcomes

三选一，并说明是哪一个：

- **clean** — 每个 feature 都做了源码与 live 覆盖；没有值得交付的改动。不建分支、不开 PR。
- **changed** — 一个 PR 交付已证实的文档、harness 或 map 修正。
- **blocked** — 覆盖无法完成，或已证实的修复无法安全交付。准确说明卡在哪。

## Edit scope

只编辑 verification skill 自身目录（其 SKILL.md、features/ 以及它拥有的 harness 脚本）。运行期间绝不改产品代码：map 描述的行为若应用已不再具备，要么是文档漂移（修 map），要么是产品回归（向用户报告，别用文档糊过去）。

## Pass

0. **定位目标。** 找到要维护的 verification skill：正文含 launch/drive 段与 feature map 的项目本地 skill（通常是 `.cursor/skills/verify-*/`）。多个候选 → 询问是哪一个；没有 → 停下并指向 `/create-verification-skill`，不要凭空造目标。

1. **索引卫生。** 读 feature map 的 README 并 glob 同级文件。修正缺失、多余、重复或失效条目。轻量即可；不要生成清单。

2. **源码波次。** 每个 feature 文件起一个只读 subagent，并发拉起。各自从源码回答「该面向用户的功能怎么工作？」、用引用标出可能的文档漂移，并返回一条精简的 live 验证配方。子 agent 不得驱动应用、不得改文件。返回形态：feature 摘要 / 源码入口 / 可能漂移或 none / 一条 recipe。

3. **对齐汇总。** 每个 feature 文件都应有返回摘要。将重叠 recipe 合并为尽可能少的应用状态。抽查已引用漂移；不必重证干净结论。扫最近变更中 map 未收录的面向用户界面——在宣称缺失前须有具体源码路径。

4. **Live 演练。** 即便源码侧看起来干净也必做。协调者独占驱动；遵循 verification skill 自带的 launch 模型——一个长生命周期实例串行驱动 server 与 UI，或每次 drive 用新的隔离会话（短生命周期 CLI 由该 skill 的 Launch 段决定，不由本 skill 决定）。每个 feature 至少演练一次；整次 pass 无论失败与否保持三条不变式：(1) 不要驱动自上次出现异常以来未做健康检查的实例——首次 drive 前 doctor，以 session 为单元时每个新 session doctor，任一 failed drive 后再 doctor；doctor 看不见失败时（进程健康但 UI 卡死），重置到已知状态或重启，别赌；(2) 迄今捕获的证据在每次清理后仍在其命名位置可查，不能想当然；(3) 某次 drive 启动的东西不能活过该 drive 的用途——失败迭代的残留要清理，无论 session 卡住、退出还是共享（共享实例时清残留，不删实例）。因 skill 漂移导致的 doctor 失败算漂移：在 edit scope 内修复并重试一次——仅重启修复所失效的部分——再称 pass 为 `blocked`。无法触达的 feature 仅在具备具体前置（auth、entitlement、OS、外部状态）且说明尝试路径时标 `verified-unreachable`；map 漏写该前置即漂移。分诊产生的 harness 修复交付前须再 live 重跑。最终 teardown 在整次 run 的最后一次 drive 之后——含那些 re-proof——使无物活过 run（证据按 skill 保留）。

5. **分诊。** 用户视角描述错或缺 → 文档漂移，修它。行为正常但 harness 驱动不了 → harness 缺口，修它；harness 修复遵循与生成相同的 helpers 规则（脚本可执行、调用方式写在 skill 正文）。应用行为确实坏了 → 产品缺口；记下来给用户，别放进本 PR。

6. **交付或停止。** **changed**：一个 PR 的已证实修正，先重读每个改动文件。**clean** 或 **blocked**：不开 PR，如实报告 outcome 与覆盖情况。

在 scratch 位置保留简短运行笔记（覆盖的 feature、不可达前置、确认漂移、outcome）；不要 commit。
