---
name: arena
description: "对同一任务并行 spawn N 个候选方案，选定基线，再把失败方中最强的部分嫁接到基线上。用于 /arena、'arena this'、'throw it in the arena'，或当单次尝试非平凡产物会锁死错误形态时。"
disable-model-invocation: true
---

# Arena

在启动任何东西之前，先打开 todolist，每个阶段一条（Frame、Fan out、Cross-judge、Pick、Graft、Verify）。

## Phase A: Frame

每个候选收到相同 prompt，因此 prompt 即契约。

1. 说明每个候选要产出的 artifact。
2. 推导 rubric：针对*本*任务的成功形态，给出 3–6 条可具体打分的标准。它是 Phase D 挑选者的工具；候选只看任务本身。
3. 选定 runners。使用 `~/.cursor/rules/pstack-models.mdc` 中的 `arena runners` 行。若规则或该行缺失，默认各一台：`claude-opus-5-5-xhigh` 与 `grok-4.7-xhigh-fast`。该行中的 `auto` 或 `inherit-parent` 表示父模型，对应席位省略 `model`。若 Task 工具拒绝某条配置，该席位改跑其家族的默认并说明。家族按前缀：`claude-*`，以及 `grok-*`（含 `cursor-grok-*`、`composer-*`）。被拒绝的 `composer-*` 改跑 `grok-4.7-xhigh-fast`。若家族无默认，或 Task 工具拒绝该默认，从其错误信息中选同家族最接近的合法 slug，或无法则撤席并说明。撤席后若少于两台 runner，补足到两台；若该行只有一条则一台亦可。追加的 runner 使用其余 runner 的 model，若无剩余则省略 `model` 以父模型运行并说明。arena 覆盖多个设计方向时多加 runner。工作偏生成而非判断敏感时，可对同一 model 跑 N 次。按 **poteto-mode** skill 的 Subagents 一节，为每个 subagent 选择推理等级与 fast / standard 速度。
4. 给每个候选单独的输出位置（尽量用 git worktree，否则 `/tmp/arena-<slug>/candidate-<n>/`），遵循 **separate-before-serializing-shared-state** principle skill。

## Phase B: Fan out

在同一条消息里 spawn 全部 N 个 subagent，`run_in_background: true`，各自带上任务、共享 grounding 路径、自己的输出路径，以及产出 artifact 并简短说明考虑过哪些备选、拒绝了什么的指令。

若某候选没有产出，以 N-1 继续并注明 dropout。

## Phase C: Cross-judge

Phase B 全部候选完成后，从 `~/.cursor/rules/pstack-models.mdc` 的 `arena cross-judge pool` 行选一个 model。若规则或该行缺失，从 `claude-opus-5-5-xhigh` 与 `grok-4.7-xhigh-fast` 中选。spawn 一个只读 judge subagent。judge 绝不能来自父模型家族（家族定义见 Phase A 第 3 步）。只按 pool 顺序尝试另一家族的条目。`auto` 或 `inherit-parent` 表示父模型，永不担任 judge。默认 pool 下，Claude 父模型用 `grok-4.7-xhigh-fast`；其他任何家族的父模型优先试 `claude-opus-5-5-xhigh`。若 Task 工具拒绝某条，从错误信息中选同 model 最接近等级或速度的合法 slug，否则试下一条。若另一家族无一可跑，再按同样方式试默认 pool 中另一家族的条目。若仍无一可跑，跳过 judge，Phase D 单独选基线并说明。若不知父 model，按 pool 顺序试真实 slug 并说明未做家族校验。judge 看到 rubric 与按路径标注的候选，逐条标准打分并推荐基线及理由。它与你在 Phase D 的阅读并行；候选仍在写入时不要 spawn judge。

## Phase D: Pick a base

选定前通读每个候选。按 rubric 逐条打分，不要靠整体感觉；若 Phase C 跑了 judge，与其结论对照。基线一致则确认选择。不一致说明一方有偏或 rubric 含糊。做决定前读双方理由。

选未来维护者最易扩展、且不易破坏不变量的基线。两者势均力敌时，按 Laziness Protocol 优先更干净的边界或更小的 API。

## Phase E: Graft

对每个失败候选再扫一遍，找出值得迁入基线的部分——通常每个候选一两处，而非大部分。按 **redesign-from-first-principles** principle skill 手工完成每次嫁接，使结果仍在一个心智模型下自洽。不要机械粘贴。

若候选收敛到同一形态，说明共识强，记下并交付共识形态，无需 graft。若严重发散，说明 Phase A 规格不足；重新 frame 并重跑，不要对发散取平均。

## Phase F: Verify

像验证任何其他产出一样验证合成 artifact，遵循 **prove-it-works** principle skill。若验证发现 arena 遗漏的问题，要么 Phase A 错了（重新 frame 并重跑），要么某候选抓住而你漏了 graft（回到 Phase E）。不要用粉饰掩盖。

## Outputs

一个合成 artifact，旁附简短 synthesis note：基线及原因、cross-judge 裁决或 Phase C 跳过 judge 的说明、每次 graft 及其来源候选、被拒绝内容及原因、任何收敛或 dropout，以及验证结果。
