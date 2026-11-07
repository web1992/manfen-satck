---
name: blast-radius
description: "在交付前找出变更可能在别处搞坏什么——超出 diff 范围——并通过跑真实代码而非写说明来证明它安全所依赖的那一条事实。用于「X 的 blast radius」「这可能搞坏什么」，或不信任的小 diff 审查。"
disable-model-invocation: true
---

# Blast radius

在交付前找出变更在别处会破坏什么。用于「blast radius of X」「what could this break」，或审查你尚不信任的小 diff。

与 `how`、`why` 配套。`how` 讲代码做什么。`why` 讲为何长成这样。Blast radius 讲它在别处会破坏什么。

列调用方不是本 job。agent 一秒就能 grep。job 是 grep 看不出来的破坏。

## Don't trust your own writeup

听起来对的 blast-radius 说明毫无价值。写得再像真的也不代表真。所以不要交 writeup。找出整件事依赖的一两条事实，用跑代码来证明。

### How sure are you

对变更安全性依赖的每条事实，尽可能廉价地往下走这条阶梯，并说明停在哪一级。

1. 你这么说。单独一文不值。
2. 你指向那一行。真实的 `file:line`，或库自身源码。
3. 你说明坏情况不会发生。逐步走失败路径，走不到。
4. 你跑过了。脚本或 test 调用真实代码，错了就大声失败。
5. 你在运行中的应用里复现了。

第 4 步通常是一个小脚本：import 应用随发的同一库，调用你担心的那个函数。

## Steps

1. 读变更。diff、新增/改动/删除的符号，以及现在行为有何不同，含 diff 未写明的部分。用 `why` 的步骤 2 拉 PR 与 commit。
2. 找出它安全所依赖的那一条事实。多数看起来危险的变更只因一条事实而安全，例如「这次调用只丢掉已死的 cache 项，不做别的事」。找到它。若成立，多数危险情形一次清空。时间花在这里，别花在长长的 maybe 列表。
3. 看 grep 止步之处。读所调库的源码，查 pinned 版本与本地 patch。弄清何时运行：microtask、unmount 与 teardown、Solid 对 React。跟 symbol 搜索漏掉的：API 返回的 JSON、DB 列、线格式、另一语言读同一字节、feature flag、下游三跳的代码。
4. 诚实评估每条风险。给真实发生概率与真实代价。保留已确认风险。单独列出查过并排除的。规则同 `why`。引用真实 `file:line`；搜不到仍是答案；绝不编造 caller 或 API。
5. 证明那一条事实。写脚本或 test 跑真实代码，运行，粘贴结果。
6. 又大又宽的变更，用 `arena` 跑。向多个 model 问同一问题并合并答案。不同 model 会抓到不同真实 bug。

## What to hand back

- **做了什么。** 改了什么，含不明显部分。
- **安全所依赖的那一条事实。** 陈述它，说明推到第几级，展示证明。证明不了则写 unproven。
- **风险。** 每条写明如何破坏、`file:line`、可能性与严重性、如何检查。重要的贴上证明。
- **已排除。** 查过什么、为何没事。
- **合并前。** 能抓到真实 bug 的最便宜 test 或 repro，含你写的脚本。

经 `unslop` 写就，引用真实代码，公开前剔除私密内容。

**Reply：** 上述 writeup，那条安全事实要么已证明要么标 unproven。
