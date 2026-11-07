---
name: principle-build-the-lever
description: "适用于任何非琐碎工作，不限于批量：编辑、迁移、分析、检查。造工具来执行或证明（codemod、script、generator，或 subagent 遵循的 skill），而非手做。工具是 reviewer 可重跑的产物。"
disable-model-invocation: true
---
# Build the Lever

工作非琐碎时，造执行它的工具，而不是亲手做。

codemod、generator 或 script 每次以同样方式干活，重跑零成本。工具是一份 reviewer 可读、可重跑以核对工作的产物。手改只能重做才能再验证。确定性 script 把「相信我」变成「跑这个」。

默认造杠杆。仅当任务琐碎、一眼能看清的两三处明显编辑时可跳过。

- 先手做第一个单元以摸清配方，再造工具。在该单元上重跑并与手做版 diff 以证明。让杠杆可安全重跑。
- 编辑用 codemod 或 script，重复文件用 generator，分析用 dump-to-sqlite 查询，验证用可重跑检查。
- 确定性杠杆优于 fan-out。若工具能一次 pass 处理每个单元，自己跑。不要把 script 能做的事 fan-out 给 delegate 手改。
- fan-out 给 subagent 时，把杠杆写成它们共读的 skill：配方、验证契约、do-not-touch 围栏集中在一份产物里。放在 delegate 写 scope 外，免得悄悄改契约。
- 援引本原则应产出文件。若援引了而 diff 里没有 codemod、script、generator 或 delegate skill，则未应用本原则。
- 工作会跨 session 时 commit 杠杆。

门槛是琐碎与否，不是重复次数。一次性工作仍值得造杠杆，当杠杆让工作可核对时。按 [Laziness Protocol](../principle-laziness-protocol/SKILL.md)，造完成或证明工作的最小 script，不要造 framework。

区别于 [Encode Lessons in Structure](../principle-encode-lessons-in-structure/SKILL.md)——后者把反复出现的指令变成持久 guardrail。本原则是眼前工作的吞吐与可审性。若要 script 化验证本身，见 [Prove It Works](../principle-prove-it-works/SKILL.md)。
