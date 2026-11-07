---
name: technical-writing
description: "分层技术写作标准：Diátaxis 结构、Google 开发者风格句式、STE 指令规则、Global English 语法。用于 /technical-writing，或在撰写或审阅文档、RFC、readme、PR 描述、commit 消息时使用。"
disable-model-invocation: true
---

# Technical writing

让疲惫的工程师第一次读就能懂。四层标准都要用上。

三层之上还有三条总规则：

- **删掉每一个不起作用的字。** 拿掉某个词句子仍能成立，这个词就删。「In order to」就是「to」。「It is important to note that」等于什么都没说。
- **用短、日常的词。** 用 use，别用 utilize。用 help，别用 facilitate。用 do，别用 perform。长词必须靠精确换来长度。
- **某条规则会让句子变差时，换别的方式改句，或别硬套规则。** 条条遵守却像机器写的句子，就是失败。

代码库就是词表。写真实的符号、文件、flag 或命令名，不要同义词，也不要绕着描述。

别发明行话。用开发者会脱口而出的说法：「move」「delete」「a budget that only decreases」，别用 evacuate、ratchet、endgame。文档第一次在讲清含义时，给具名模式命名可以。在回复里把新发现的 offender 及其替换提议为对 `unslop` 的 abstract-metaphor 规则的增补，并附上 diff。不要改那个 skill。

## Vary the rhythm

文档可以每层都遵守，读起来仍像机器写的：每句都掐短、没有任何观点、什么都不具体。

- 有意识地混用句长。短句落点。长句带上事实及其条件或后果。
- 一句一意不等于句句同长。承载两个想法的句子拆开。只承载一个想法的长句留着。
- 模式允许的地方要有观点。Explanation 权衡取舍，就说出你怎么看待，而不是列 pros and cons。Reference 保持干涩。
- 具体胜过空洞 sterile。别写「schema changes can cause issues」，写「a column rename fails the build」。

## Pick the mode first (Diátaxis)

一篇文档，一种 mode。两个问题定它：内容是促进行动（doing）还是促进理解（thinking），以及服务学习还是手头工作。

- Action + learning：**tutorial**。
- Action + work：**how-to**。
- Understanding + work：**reference**。
- Understanding + learning：**explanation**。

compass 可用于整篇文档，也可用于单句。

**Tutorial: learning by doing.** 学习者的成功是你的责任，不是他们的。开头说清楚学习者会做出什么，而不是他们会「learn」什么。每一步都要产生可见结果，早且勤。告诉他们应该看到什么：预期输出、prompt 变化、日志行。解释压到一句从句加链接。保持具体。用「we」、用命令式写：「First, do x. Now, do y.」

**How-to: steps to a goal.** 解决人面临的问题，不是机器能执行的操作。假定读者有能力，别上课。只有动作：不跑题、不铺背景、不为完整而完整。那些用链接代替。允许分支与判断：「If you want x, do y.」用任务命名指南：「How to calibrate the radar array」，不是「Radar array calibration」。

**Reference: facts for lookup.** 只描述。不指导、不说服、不给意见。干涩、完整、确定。陈述事实、选项、限制与错误，不 hedging。镜像被描述物的结构，让代码与文档能一起导航。把材料放在读者预期之处。能的话从代码生成。

**Explanation: understanding and why.** 一个边界清晰的话题，离开产品也能读。每个标题前面都能隐式加上「About...」。锚在一个真实的 why 问题。给上下文：设计决策、历史、约束、备选。意见只在这里允许，别处不允许。

别混 mode：tutorial 里别塞 reference 表，reference 里别塞 tutorial 手把手，how-to 里别争论。拆开并链接。

## Write sentences to the reader (Google developer style)

- 对读者用「you」，现在时。「Will」只用于确实稍后才发生的事。
- 说清楚谁做什么：「the compiler checks」，不是「is checked」。只有 actor 未知或无关紧要时被动才合适。
- 指令写成命令：「Click Submit.」事实平铺直叙。永远不要「should be done」。
- 条件放在指令前：「To delete the document, click Delete.」
- 常见情况先说。例外放后。
- 像懂行的朋友说话。不要 buzzword、比喻、指令里的「please」，流程里也不要「simply」「easy」「quickly」。真那么简单，读者不会在这里。
- 别预告（「we will soon support...」），也别连续两句同一开头。
- 链接用文字说明去向：页面标题或短描述。绝不「click here」。能在一页上给一句上下文，就别链到外面。
- 标题承载要点，不只是话题（「Pick the mode first」，不是「Modes」）。Sentence case。任务标题是裸动词短语（「Create an instance」）。概念标题是名词短语。每页一个 h1，不跳级。
- 顺序用编号列表，其余用 bullets。列表前用完整句引入。条目保持平行。
- 代码用 code font。UI 元素加粗。用 serial comma。别用「etc.」，开头就说明列表不完整。

## Make statements load one at a time (STE rules)

- 一句一条指令。其余地方一句一个想法。
- 指令大约超过 20 词、其他句子大约超过 25 词就拆。
- 警告或条件放在它所守护的步骤前：「If hot oil touches your skin, injuries can occur.」
- 保留 the 和 a：「Remove backup file」有两种读法。「Remove the backup file」只有一种。
- 每个词一个含义、一个职责，然后坚持。check 若表示 inspect，就别也用来表示 restrain。
- 每个动作选一个词并坚持用：「start」，别这里 start、那里 initiate。
- 流程写成直接命令，不要叙述式，不要被动：「Install the component」，不是「the component must be installed」。
- 能不用「-ing」词就不用。

## Leave no sentence open to two readings (Global English)

- 把 only、not 等词贴在它们修饰的词旁：「only fails on growth」和「fails only on growth」意思不同。
- 拆开长名词串：「the proto import budget check script」改成「the script that checks the proto-import budget」。
- 每个 it、they、this 都指向明确一物。拿不准就重复名词。别用 this 或 which 指整句。
- 别省动词：「Phase 1 moves the converters and Phase 2 the runtime」让 Phase 2 没动词。补上。
- 保留体现结构的小词，例如「Ensure that the switch is off」里的 that。不要为了字数牺牲清晰。
- 并列时重复冠词以防误读：「the client and the host」，不是「the client and host」，当指两样东西时。
- 一句能两种分组时，说清 and 或 or 连的是哪几块。「Both...and」「either...or」「if...then」是免费的消歧手段。
- 用句号，不用分号。破折号改成新句。
- 括号内文字要么是完整语法单元，要么自成一句。别用「(s)」造复数。
- 不用斜杠：写「a, b, or both」，别写「a/b」或「and/or」。
- 各处对同一事物只用一个名字。一篇文档对同一物说「the gate」「the ratchet」「the budget check」，等于教了三样东西。没变的别来回换名。
- 跳过习语、口语、拉丁缩写和比喻。

## Voice and repo specifics

- 本 skill 碰到的每份文档都套用 **unslop** skill。该 skill 拥有 slop 模式目录：AI 词汇、filler、hedging、格式痕迹。
- PR 描述和 commit 消息也是写作。除 Diátaxis 外每层都适用。PR 正文是审阅者一分钟内能读完的简报。别粘贴 swarm 日志、SHA 列表或 metric 表。链出去。
- 产品 UI 字符串不是文档。那些用你们产品的 copy 指南。
- 代码片段用 tab 缩进。写真实路径。每个计数或树状声称在落地 commit 时为真，并写上能重新生成的命令。

## Worked example

改前：

> Configuration of the proto import ratchet budget script parameters is performed via budget.json. Note that it's important to remember that running with --write, which updates the committed budget to reflect the current count, should only be done when lowering it. If exceeded, CI fails.

改后：

> `budget.mjs` reads the committed budget from `budget.json` and counts the files that import protos. If the count exceeds the budget, CI fails. Run `budget.mjs --write` only to lower the budget.
