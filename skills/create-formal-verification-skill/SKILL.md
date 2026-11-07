---
name: create-formal-verification-skill
description: "生成项目本地 skill，在 bug 代价最高、测试最难触及之处，用合适验证工具对任意语言的代码做形式化验证。用于 /create-formal-verification-skill、「model-check this race」、「prove this invariant」，或当并发、重试、状态机、解析器、unsafe 代码需要超出测试的保障时。"
disable-model-invocation: true
---

# Create a formal verification skill

测试只覆盖有人想到要写的用例。形式化验证覆盖陈述范围内的一切用例：模型中每一步的每一种顺序、有界内的每一种输入，或全部输入。本 skill 在仓库中找到这件事最要紧的部分，验证其一项性质，并留下项目本地 skill，让下一个 agent 能对照该性质检查变更。为那些半路接手、从未见过你工作的 agent 撰写该 skill。用户必须能信任结果并据此行动，而无需学习验证语言。

## Choose the target from the code

从代码库作答；仅询问你无法自行观察的内容。若用户点名某区域或某个 bug，从那里开始。

好的目标是代码做出的一项承诺：一旦违背会伤害用户，而测试很少触达会打破它的情形。选仓库里最强的那类目标，而非最容易验证的。

## Let the question choose the tool

选能以最低成本回答问题的那类工具：model checker、proof assistant、SMT 支撑的 verifier，或其他工具。事件是否可能以糟糕顺序发生，通常适合在设计的小模型上用 model checker，任意语言下 TLA+ 是此类默认。代码本身是否对每种输入都行为正确（如内存安全、溢出、数据结构不变式），通常适合在代码上运行的 verifier。若该语言没有可运行的 verifier，如实说明，并对真实代码用生成输入测试，而非手写函数模型。性质必须在任意规模成立、有界不够时，可能需要证明。优先选仓库开发者能安装并运行的工具，并向用户说明选择理由。

## What makes it good

- **True to the code.** 模型或证明的每一部分都对应审阅者可打开的代码。验证视为不可分割的一步，在代码里也是不可分割的一步。命名来自本仓库。
- **Able to fail.** 从未见过失败的检查证明不了任何事。展示各性质在它守护的代码的破损变体上失败，例如删掉检查或打乱步骤顺序。
- **Findings stay findings.** 当前代码违背性质时，用代码术语报告并给出反例。不要削弱性质或模型直到通过，也不要改产品代码，除非用户要求。
- **Honest about scope.** 说明验证了什么、在哪些界与假设下、排除了什么。通过覆盖的是模型或界，不是整个系统。
- **Readable without the tool.** 用各一句平实话分别陈述性质与危害。用代码用语把反例说成简短的步骤序列。
- **Cheap to rerun.** 一条命令、可复现的工具版本、足够短的运行，使下一个 agent 在每次相关变更时都会用。
- **Kept in step with the code.** 记录每项检查覆盖的文件与函数，以及你上次确认验证仍描述它们时的 commit。在该 commit 之后覆盖代码变更时，下一个 agent 能看出验证需要再看一眼。

## Generate the verify skill

撰写带 frontmatter 的项目本地 skill，指明区域及何时使用。无 frontmatter 则 skill 不会注册。仅凭该 skill，下一个 agent 必须能判断变更是否触及已验证代码、运行检查、区分真实 bug 与建模错误，并在变更是有意为之时更新验证。把覆盖记录与 sync commit 写在 skill 里，并告知下一个 agent：仅在确认验证描述新 commit 上的代码后，才将 sync commit 前移。使用本仓库的真实命令、路径与名词，不留占位符。

若检查是 TLA+ 模型，告知下一个 agent 用 pstack 的 [`/statechart`](../statechart/SKILL.md) skill 阅读每条结果。它以平实语言展示 checker 发现的每个不变式违背的最短路径。检查通过时，`/statechart` 绘制系统在状态间如何移动的图。按名称引用 `/statechart`。其命令、选项与文件路径随版本与机器而变，不要把它们抄进生成的 skill 或其脚本。

## Prove it before you hand off

像下一个 agent 那样，按生成的 skill 在真实代码上做一次会破坏性质的 scratch 变更跑一遍。必须能抓住破坏。然后撤销 scratch 变更，确认检查回到起点：通过，或仅在你将报告的发现上失败。从未跑过的 verify skill 只是草稿。若 skill 漏掉破坏或某步未运行，修 skill 或验证，不要改 scratch 变更。

## Hand off

告知用户你验证了什么、用了哪种工具及原因、界、检查发现（平实话）、如何运行、未覆盖什么。仅当你发现相近第二目标时才建议。指向 `/maintain-formal-verification-skill`，以便在变更后未重检时把验证拉回一致。
