---
name: no-comments
description: "拉起 Comment Sicko，处理已接受的发现，并对声称的约束提供编码方案。"
disable-model-invocation: true
---

# No comments

拉起 Comment Sicko。对已接受的发现采取行动。

以 Comment Sicko 的崭新视角为准。

## Scope

使用调用方指定的文件或 diff。否则使用相对基分支（默认 `main`）的当前 diff，含工作区。

## Steps

1. 用 `subagent_type: "comment-sicko"` 拉起 `Task`。传入 scope。按 **poteto-mode** skill 的 Subagents 一节，为每个 subagent 选择推理等级以及 fast / standard 速度。不要复述其规则。
2. 审阅其报告与 diff。拒绝应用代码编辑、越界 scope、受例外保护的删除、表述错误的 `MUST KILL` 理由，以及把有意保留的代码标为有罪的 flag。对我们代码里意外之处的 reshape flag 保持可执行。不要恢复那些注释。保留仅在有证据证明其关于我们无法改动之物时成立。审计遗漏的 scoped lint 与 TypeScript suppression。正确性或安全性的 suppression 保持为可执行的 `MUST KILL`。仅在确有精确例外与 scoped 证明时恢复删除。在接受单薄的 `IMPORTANT` 或 `do not remove` 类 kill/keep 之前，对其 symbol 跑 `/how` 或 `/why`。kill 含糊则不恢复。keep 被反驳或仍含糊则删除。对被拒报告 revert 并重跑一次，写明失败原因。第二次仍拒则报告为 open，并让 `/no-comments` 失败。
3. 通过删除死路径、去掉参数或使用真实 API 直接修复琐碎的已接受 flag。若任一修复需要改形状，对已接受集合及周边代码跑一次 `/architect`。止于草图。Architect 定形，步骤 4 实现。
4. 在 scope 内做最小的根因修复。去掉每一个被指名的 workaround。若根因 out of scope，落地最小的 in-scope 修复并把其余报为 open。**principle-fix-root-causes** 与 **principle-redesign-from-first-principles** skill 仅作意图指引。二者都不授权扩大围栏或在围栏外修实例。切勿 bolt on 症状性 guard。
5. 约束类注释含 `do not remove`、`do not change wording` 或 `talk to X before changing`。关于我们无法改动之物的 keep 保留。提供最便宜的 in-scope 类型、runtime、test 或 CI lint 方案。等待交互式批准。无人值守与 eval 需调用方事先批准。若批准，先编码再删除。否则删除、把约束报为 open，并草拟 out-of-scope 工作。
6. 报告删除数、恢复的注释、重跑次数、architect 草图、修复、编码提议、已编码项、未强制的约束及其他 open 工作。
