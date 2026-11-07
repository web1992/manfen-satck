---
name: statechart
description: "对会在状态间迁移的系统（如产品流程、协议或服务）做 TLA+ model 检查，并绘制成 Mermaid statechart。报告给出到达各违反规则状态的最短路径。用于 /statechart，或把 bug 展示为穿越状态的路径。"
disable-model-invocation: true
---

# Statechart

图表展示系统如何在状态间迁移，用词面向阅读者。阅读者据此判断行为是否正确。

## Run

在仓库外的 scratch 目录中工作。编写 TLA+ spec 及其 config，然后运行：

```sh
<skill dir>/scripts/statechart SPEC.tla [--config FILE] [--view FILE] [--out DIR]
```

`scripts/statechart.ts` 是 TypeScript，需用能执行 TypeScript 的运行时运行。

首次运行会通过 mise 安装 TLC 与 Java，需要网络。

## Write the spec

- 把阅读者认同的每条规则写成 INVARIANT。规则可关于某屏、某结果、某存储值或某消息。若 model 没有对应变量的规则，补上变量。永远不要为让规则成立而削弱规则。
- 将 `Next` 写成各 action 的析取，例如 `YouPressPlay \/ YouPressStop`。否则 TLC 会把每一步都命名为 `Next`。
- 没有出边的状态会打破 `NoDeadlock`，即使它是正确的终态。若已有同名 INVARIANT 或 PROPERTY，规则名为 `CHECK_DEADLOCK`。在 `Next` 中增加仅在终态为真且不改变任何事的 action，例如 `Done == phase \in {"paid", "failed"} /\ UNCHANGED vars`，并在 `step()` 中给它 label。

## Pick the chart

需要判断力的工作由你完成：各状态与箭头上的措辞、阶段及其颜色、标题，以及给阅读者的报告。把成果放在 view module 中，并用 `--view` 传入。

要展示阅读者的用语，view 导出 `state(vars)`。`state()` 的返回值决定图表形式：

- 字符串 → 扁平图：`export const state = (v) => (v.paid ? "Paid" : "Paying");`
- 数组 → 父状态：`export const state = (v) => v.main.split(".");`
- 对象 → 每个 key 一个并行 region：`export const state = (v) => ({ Playback: v.playback, Volume: v.volume });`

用阅读者能看见或关心的内容标注各状态，例如「Paying」或「Lease held, write pending」。label 中不要出现变量名。在 `vars` 里，TLA+ 序列是键从 1 起的对象，故 `v.pc[1]` 即 `pc[1]`。集合是数组。

view 还可导出 `step(action, from, to)`。它接收 TLC action 名与步进前后的值，返回箭头 label。每一步 label 按用户所见写成，例如「you press play」或「the connect fails」。不要写代码名或 TLA+ action 名。在图上结局相同的两条 action 用同一 label，从而共用一条箭头。

某步仅在部分条件下可发生时，在 label 末尾用方括号写条件，用阅读者的措辞，例如 `you press play [while the file loads]`。在 regions 图中，命令会在箭头上用其他 region label 的措辞写条件，例如 `[when Offline]`。你的方括号会替换它，于是从同一状态出发的两条箭头可能看起来相同。因此在 regions 图中不要为另一 region 上的条件写方括号。当某个 label 未展示的值决定步进去向时，在 label 中写明结果，例如「the roster read fails」。

view 还可导出：

- `group(vars)` 返回阶段名。每个图表状态使用该 group 的颜色。当一个图表状态背后的 model 状态分属不同 group 时，该状态不着色。
- `colors` 为每个 group 名指定 color class。
- `title` 为图表顶部显示的字符串。

命令对每个 model 状态最多调用一次 `state()` 与 `group()`，对每个 step 最多调用一次 `step()`。图表与报告共用这些结果，因此每个函数应只依赖其参数。

图表不绘制停留在同一 label 内的 step。当阅读者必须看到此类 step 时，拆分 label。

目标大约 20 条箭头或更少，更密的图难以阅读。先让 `state()` 返回更粗的 label。对表现相同的值共用一个 label，省略阅读者不需要的细节，例如返回不改变任何事的在途调用。保留 bug path 上的每个状态与每个 mark。合并若把 `breaks` 状态变成 `mayBreak`，会掩盖规则在何处被打破。不要合并打破不同规则的两状态，否则阅读者无法看出哪条规则在何处打破。若图仍过密，对阅读者不需要的 step 从 `step()` 返回 `null`，例如每个状态都有的 reset，或箭头重复其他箭头信息的 step。隐藏 step 时遵守：

- 每个图表状态仍须能从起始状态经箭头到达。
- 每个在 model 中有出边的图表状态至少保留一条出箭头。
- 隐藏某箭头的全部 step，或全不隐藏。只隐藏部分会使其条件变窄。
- 在 hand-off 中说明图表隐藏了哪些 step。

## Read the report

- 图表中，打破规则的状态带有 class `breaks`。仅其背后部分 model 状态打破规则时，带有 class `mayBreak`。Legend 标出各 mark 对应的规则。两条规则同时打破时，一个 mark 上列出两者，因而在 hand-off 中说明哪个状态打破哪条规则。在 regions 图中，当包含某状态的每种 region 状态组合都能打破规则时，该状态获得 mark。某组合能打破规则但其状态均无 mark 时，该组合中每个状态都获得 mark。
- TLC 会越过打破规则的状态，因此图表会显示 bug 之后的状态。
- 正因 TLC 会越过 bug，对状态数无上限的 model 可能永远跑不完。保持 constant 较小，或添加 CONSTRAINT。
- 使用 VIEW 或 SYMMETRY 时，TLC 把视为相同的状态计为一个，图表只画该状态。箭头可能指向它而非 step 实际到达的状态，报告会说明。
- TLC 会在越过 CONSTRAINT 或 ACTION_CONSTRAINT 后的每个状态检查规则，但不会探索该状态或检查 deadlock。当某条规则的最短路径止于此类状态时，图表画出该状态，报告给出该路径。图表不画界限之后的其他状态。此类状态中的 deadlock 不会显示。TLC 也会在 ACTION_CONSTRAINT 排除的每步之后检查规则，即使它通过另一条路径到达下一状态。当某条规则的最短路径以此类 step 结束时，图表画出该 step。
- 命令按所写 config 用单 worker 通过 TLC 检查规则。某条规则打破时，命令移除该规则并再次运行 TLC，直至无规则打破。首次打破之后，每次运行在遇到 100,000 个不同状态时停止，以免大 model 永无止境。运行触及该界限或因错误停止时，报告在「Rules not checked」下列出未检查的完整规则，图表不对其做 mark。未检查的 rule 可能成立也可能打破。要检查它，从 config 移除已打破的规则，或缩小 model。该界限会加入一个改变 TLC 变量顺序的 module，因此对某 property，TLC 可能找到与无界限运行不同的反例。
- `tlc.out` 是 TLC 按所写 config 打印的内容。若图表未显示某条已打破规则的 bug path，`tlc-RULE.out` 是找到该路径那次运行的 TLC 输出。当 TLC 重复其第一条 warning 时，纯 TLC 会打印两次，但 `tlc.out` 只显示一次，因为 TLC 不把第二份副本放进本命令读取的输出。这仅当 Java 以 `-XX:+UseParallelGC` 运行时发生；否则 TLC 的第一条 warning 是建议传入该 flag 的提示，只打印一次。

## Show the chart

以 `mermaid` 代码块向用户展示 `chart.mmd`。图表预算为 500 个状态，按所写 config 由 TLC 计数。超过时，图表只绘制通向打破规则的路径；若无规则打破，命令不写 `chart.mmd`。它仍会检查每条规则，报告给出状态数与各 bug path。要看全图，缩小 model。

用 Pele 渲染图表。用户要图片，或图太大不便以文本阅读时，遵循 `references/render.md`。除非用户要求，不要用其他 renderer。

## Hand off

把每张图及其 Mermaid 文本交给需要做决定的人。每个 bug 加一行：规则、阅读者对该规则的句子，以及报告编号的路径。说明 model 省略了什么。给出用新 `--out` 目录重新生成文件的命令。

当 model 描述代码时，在真实代码上重放每条 bug path，作为测试或 live repro，每个 action 一步。若 bug 未出现，在能证明 model 没错之前，先假定 model 有误。
