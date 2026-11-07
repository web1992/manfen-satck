---
name: automate-me
description: "用于「automate me」「创建/更新/刷新我的 -mode skill」「把偏好或工作方式固化成 skill」，或希望 agent 按用户方式工作。通过 create-skill + unslop 起草或修订个人 -mode skill，可选从近期 transcript 拉新证据。"
disable-model-invocation: true
---

# Automate me

引导式流程：把用户的工作惯例变成 agent 会遵守的 skill。产出是一份为其定制的 `-mode` skill（如 `jay-mode`、`priya-mode`）。

本 skill 编排另外三个：内联挖掘（见步骤 1）、Cursor 内置 `create-skill`（撰写）、**unslop** skill（散文纪律）。负责顺序，不替代它们。

## Flow

### 0. Check for an existing skill

递归查找 `.cursor/skills/**/*-mode/SKILL.md` 与 `~/.cursor/skills/*-mode/SKILL.md`，匹配用户 handle。Mode skill 可在个人分类目录（`.cursor/skills/<handle>/`），不必只在顶层。若已存在，用 `AskQuestion` 确认意图（除非用户已说「update my skill」之类）：

- 更新现有 skill（重复运行的默认）
- 从头开始（少见，先问原因）

更新模式会改变后续流程：
- 步骤 1 只挖掘 skill 上次编辑之后的记录（`git log -1 --format=%cI <path>`）。
- 步骤 2 问什么变了或缺什么，不是从零问要捕获什么。
- 步骤 4 就地编辑现有文件。用户未推翻的章节保留。有新证据的章节修订。仅对真正新规则加新章节。

### 1. Mine their history

展开前先定位当前 workspace 的 transcript。系统 prompt 会给出 workspace 的 `agent-transcripts/` 目录。只用该路径。不要 glob `~/.cursor/projects/*/`。那会跨 workspace 边界并读到无关项目的私密对话。

在该范围内浏览近期 agent 对话中的反复模式。多个并行 subagent 切分历史（如最近 2–4 周，拆成 3 段使每段有足够素材）。按 **poteto-mode** skill 的 Subagents 一节为每个 subagent 选推理等级与 fast / standard 速度。每段挖掘 subagent 从父级提供的 workspace 作用域路径读 transcript，找下列信号，返回带证据指针的短结构化模式列表。默认值得 hunting 的信号：

- 回复偏好（长度、语气、格式、「dumb it down」类纠正）
- 委派习惯（subagent、模型、专用工作流、并行）
- 验证姿态（「完成」指什么、unit test 对 live repro、reviewer）
- 代码与散文纪律（风格、引用的原则、lint / format 工具）
- 流程惯例（worktree、commit、PR、review / merge 工具）
- 元偏好（任务中改 skill、提议新 skill）

升格信号前跨 slice 交叉核对。2+ slice 见到的模式为高置信。孤立信号弱，通常丢弃。

### 2. Ask the user directly

挖掘抓不到尚未浮现的意图。用 `AskQuestion` 工具（结构化多选），别让用户从零打字。

形态：一两道题，每题 4–6 个选项，类别题设 `allow_multiple: true`。先宽（「哪些领域最重要？」），再对选中领域用具体选项追问。结构化轮次后，一道开放式聊天题接住选项没盖住的。

别一次甩 20 道题。

### 3. Cluster findings

把合并信号分组成章。常见分组（仅用适用的）：

- **Response style**：长度、语气、格式。
- **Autonomy**：多少事不问就做、MCP 工具使用。
- **Understand first**：定范围或调查变更时该用哪些 skill。
- **Subagents**：默认、并行、model 与任务、专用工作流。
- **Prose / code discipline**：原则、lint 工具、style guide。
- **Review and verify**：repro 姿态、验证 skill、live-testing 工具。
- **Process**：git worktree、commit、PR、review / merge 工具。
- **Skills**：写 skill 的习惯、先修 skill、提议新 skill。

**poteto-mode** skill 展示粒度形态。读它看细度。不要抄内容。用户的规则与 poteto-mode 不同。

### 4. Draft the skill

用 Cursor 内置 `create-skill` skill 撰写。放置：

- 路径：保留已有 mode skill 的分类。新建 mode 时，若仓库已为该 handle 建立个人分类，用 `.cursor/skills/<handle>/<handle>-mode/SKILL.md`。否则默认项目内 `.cursor/skills/<handle>-mode/SKILL.md`（或用户要个人 skill 时用 `~/.cursor/skills/<handle>-mode/`）。
- Handle：用户名或自选标识。
- Frontmatter `description`：以其名字 + `/<handle>-mode` +「按其风格工作」触发，不要用「write code」「review PR」等泛关键词。
- Frontmatter 格式：遵守 `create-skill` 的 YAML 规则。`description` 保持为单个 YAML 标量。需要时用引号或 `description: >-` 加缩进续行。
- Frontmatter `disable-model-invocation: true` 为默认。仅当用户明确希望 mode 每轮都生效时关闭。

### 5. Iterate on prose

对每一行套用 **unslop** skill 与 `create-skill` 的写作指南。

把草稿给用户并收反馈。预期多轮。狠删。Mode skill 不是手册。

### 6. Land it

在 main 外的 worktree 里工作。Commit 并开 PR。不要直接推 main。

## Guardrails

- **别过拟合单次对话。** 说过一次又在别次推翻的偏好是噪声。编码前要多实例。
- **别耍聪明。** 复述其他 skill 内容、发明比喻、给 agent 读者写「诗性」散文，是成本无收益。保持可执行。
- **引用，不内联。** 用户依赖的其他 skill 用路径引用，不粘贴摘录。他们别处维护的原则文档同理。
- **章节尽量少。** 只有用户在该处有具体、非默认规则时才加章。「Communicate clearly」不配成章。「Short paragraphs. Tables when comparing options. Bullets only when items are genuinely parallel.」才配。
- **命名约定通用化。** 祈使句用「the user」或「the human」，不用作者名字。
- **别强求对称。** 用户没有值得写下的流程规则时，整个 Process 节可以跳过。

## Evaluation

`-mode` skill 是主观产出。`create-skill` 式 test / iterate 基准循环在这里没用。与用户 vibe-check：像不像他？漏了什么？然后 ship。

仅当实践中 skill 的触发准确度成问题时，才跑 description 优化循环。

## When not to use

- 用户要任务专用 skill（非工作惯例）：单独 `create-skill`，不必挖掘。
- 用户要捕获窄工作流（如「我怎么写 commit 消息」）。那是普通 skill，不是 mode skill。
