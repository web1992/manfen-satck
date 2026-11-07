---
name: principle-sequence-verifiable-units
description: "用于多步工作（sweep、迁移、同类编辑批量执行）以及 commit、PR 的叠放方式。把工作拆成以可验证状态收尾的小单元，每步先绿再进下一步，并按能让 reviewer 自行重放的顺序交付。"
disable-model-invocation: true
---

# Sequence work into verifiable units

把工作排成一串小单元，每个单元以可检查的状态结束；当前单元未变绿前不前进。

**Why:** 在造成破坏的那个单元就被抓住时，定位很便宜。等一批做完才发现，破坏已埋在底下，你又在那上面继续堆。把同样单元排成 reviewer 能重放的交付顺序，就能把「相信我」变成「看它变红，再变绿」。

**Execution.** 在 sweep、迁移或任何同类编辑批量执行中，启动下一项前先验证当前变更。每个单元是前后括起来的：已知良好状态 → 一处变更 → 跑检查 → 再前进。先 rebase 到干净 trunk，让每个检查都相对真实基线度量。若用 lever 做编辑，单单元检查几乎免费——仍要跑。

**Delivery.** 按能证明工作的顺序叠放 commit 与 PR。典范形态是先 failing test，再在其上叠 fix。其他叙事顺序可以是：reshape 前先做一次 subtraction、处理前先抓 baseline、功能前先搭 scaffold。每个 commit 单独落地，整段序列读起来像一条论证。

与 **prove-it-works** principle skill（让每次检查真实）以及 **build-the-lever** principle skill（让单单元检查便宜）相辅相成。
