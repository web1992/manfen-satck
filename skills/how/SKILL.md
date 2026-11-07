---
name: how
description: "用于「X 怎么工作」、改代码前的走读，以及归属 / 分层问题（「该放哪」「哪个包管这个」「这是不是正确的层」）。解释子系统架构、运行时流程、上手心智模型。动机类问题用 why。"
disable-model-invocation: true
---

# How

探索代码库，回答「X 怎么工作？」类问题。产出的架构说明应达到资深工程师上手某子系统的级别：足以建立可用的心智模型，但又不要写成带注释的源码。

每一次拉起都是 Task 子 agent，`subagent_type: generalPurpose` 且 `readonly: true`。Explorer 使用 `pstack-models.mdc` 规则里的 `how explorer` 行（默认 `grok-4.7-xhigh-fast`），每个 explainer 使用 `how explainer` 行（默认 `claude-opus-5-5-xhigh`）。把 `model` 设为该行的值；若规则或该行缺失，则用默认值。值为 `auto` 或 `inherit-parent` 时不要设置 `model`。若 Task 工具拒绝某个 slug，改用默认并说明。若默认也被拒绝，从其错误信息里选同系列最接近的合法 slug。按 **poteto-mode** skill 的 Subagents 一节，为每个子 agent 选择推理等级以及 fast / standard 速度。

## 1. Assess complexity

若范围含糊，先说明你的理解再探索。用户可以纠正。

- **Simple**（单个模块、小工具，或「函数 X 怎么工作」这类窄问题）：不拉 explorer。拉起一个 explainer，一轮内既探索又解释，基于 `references/explainer-prompt.md` 构建，且不含 explorer-findings 段。转到步骤 4。
- **Complex**（跨多个文件或服务的子系统、横切特性、完整架构总览）：转到步骤 2。

拿不准时走 simple 路径。

## 2. Explore（仅 complex）

把问题拆成 2 到 4 个角度，每个角度是子系统的一个独立切片。在同一条消息里拉起全部 explorers，各自带上 `references/explorer-prompt.md` 并填好其角度。

## 3. Synthesize（仅 complex）

等所有 explorers 返回后，拉起一个 explainer，带上 `references/explainer-prompt.md`，并填入每个 explorer 的发现。

## 4. Present

呈现 explainer 的输出。为清晰度或对话上下文做轻度编辑可以，不要大幅改写。

## Output format

说明使用 `references/explainer-prompt.md` 中定义的章节，不适用的可省略：Overview、Key Concepts、How It Works、Where Things Live、Gotchas。
