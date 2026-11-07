---
name: swarm
description: "拉起 N 个并行 worker，收齐结果后输出一份报告。用于 /swarm、「swarm this」，或并行覆盖、race、gauntlet 与探索。"
disable-model-invocation: true
---

# Swarm

## Start

在启动任何东西之前，先开一个 todolist，每个阶段一条。

1. Frame
2. Fan out
3. Aggregate
4. Report

## Phase A: Frame

1. 写明完成谓词，以及 swarm 必须返回的产物或报告。
2. 选定形态。按切片分区、让 N 个 worker 在相同 brief 上 race，或两者混合。对 race 或混合形态，在 spawn 前声明 `first pass`、`rank all` 或 `best-of`。
3. 从 `~/.cursor/rules/pstack-models.mdc` 的 `swarm workers` 行选取 worker 模型。若规则或该行缺失，用 `grok-4.7-xhigh-fast`。对 `auto` 或 `inherit-parent`，省略 `model`，让 worker 跑在父模型上。若 Task 工具拒绝某个 slug，改用默认并说明。若默认也被拒绝，从其错误信息里选同系列最接近的合法 slug。若是 model race，事先写明每条 arm 的模型。按 **poteto-mode** skill 的 Subagents 一节，为每个 subagent 选择推理等级以及 fast / standard 速度。
4. worker 需要写入时，各自拥有可写输出。worker 要验证或度量 commit 时，每个 brief 写明确切 SHA。度量 brief 还要写明方法（样本数、单样本含义、顺序）。worker 在结果中同时记录二者。

## Phase B: Fan out

在同一条消息里拉起全部 N 个 worker，使用 `subagent_type: generalPurpose`、`environment: "cloud"`、`run_in_background: true`，以及步骤 3 的 model；对 `auto` 或 `inherit-parent` 不设 model。仅当 worker 需要访问用户本机上的东西时，用 `environment: "local"`。

worker 必须从非默认已推送分支开始时，传入 `cloud_base_branch`。

每个 brief 自洽。包含目标、范围、确切切片或 race arm、如何验证、报告什么。报告使用 `PASS`、`ISSUES` 或 `BLOCKED` 并附证据。能证明缺陷的 worker 报 `ISSUES`，列出它能证明的每一项，而不只是第一个。

若有 worker 退出，按 N-1 继续并注明。

## Phase C: Aggregate

读取终端结果。若某结果未记录其 brief 要求的 SHA 与方法，丢弃该结果并对该 worker 重 spawn 一次。第二次仍缺失则记为 gap。gap 不算 pass。对覆盖任务，每个必需切片都要有条结果。对 race，按事先声明的遴选规则处理，使用 first pass、rank all 或 best-of。不要粘贴 worker 原始输出。

## Phase D: Report

在对话中返回一份合并报告，含结果表、问题单行摘要、gap 或 dropout，以及使用时的 race 规则。
