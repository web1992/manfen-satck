---
name: interrogate
description: "用于「interrogate」「对抗式 review」「多模型 review」「challenge this」「stress test this code」「找盲点」或「tear this apart」。对手试图击穿变更或设计，仅在一次 run 能证明的发现保留。"
disable-model-invocation: true
---

# Interrogate

对手试图击穿变更或设计。发现仅在一次 run 证明后保留。报告发现，不应用修复。

## Frame

scope 是用户点名的内容。否则是从相对 merge base 与基分支的 branch diff，一直到工作区。用 **in-your-own-words** skill 结合用户消息、commit 与 PR 描述陈述意图。意图不清时问用户。

## Fan out

运行 **arena** skill 的 Phase A 步骤 3–4 与 Phase B，共四处改动。读取 `interrogate reviewers` 行以替代 `arena runners`。把 scope 的未提交改动复制到每个 worktree。每个对手以 `generalPurpose` spawn。替代 arena 的 task 与 rationale，给每个对手 scope、意图及以下 brief：

> 击穿这次变更。用针对真实代码的一次 run 证明每条发现，例如失败测试、复现 script，或显示错误行为的命令输出。对设计或计划，跑原型或探测当前树。复现留在自己的 worktree。每条发现报告标题、确切命令、打印的输出，以及意图所要求的输出。未跑过的不要写。这包括只有未来改动才会造成影响的风格、命名、结构与风险。无发现也是有效结果。

## Verify

按 arena Phase F 对每个发现重跑其命令做验证。输出显示失败且失败破坏意图时保留。其余丢弃。合并同根因的发现。

跳过 arena Phase C、D、E。对手找的是不同失败，故裁决保留全部已验证发现，而非某一对手的列表。

## Output

- **意图**：重述。
- **发现**：每条已验证发现附命令、输出、如何破坏意图、以及哪些对手找到。无一存活时写「无已验证发现」。
- **已丢弃**：每个对手丢弃的发现数量及原因。
