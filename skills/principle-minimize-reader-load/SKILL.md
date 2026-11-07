---
name: principle-minimize-reader-load
description: "在审阅或整理难以追迹的代码时采用。统计从问题到答案之间的层数，以及读者脑中须维持的隐藏状态；折叠仅单处调用的 wrapper，缩小可变作用域。"
disable-model-invocation: true
---

# Minimize Reader Load

可维护性指读者为理解代码必须付出的工作量。沿两条轴衡量：**layers to trace**（问题与答案之间的间接层数）和 **state to hold**（读者须在脑中记住的隐藏或可变异上下文）。LOC、圈复杂度以及「整洁架构」都只是代理指标；真正要紧的是 reader load。两条轴彼此独立：一个塞满 50 个 global 的扁平文件，可能和 6 层 adapter 栈一样难推理。两条都要守。这是 [Guard the Context Window](../principle-guard-the-context-window/SKILL.md) 的人类侧类比——读者的 working memory 同样有限。

- **折叠得不偿失的层**：仅单处调用的 wrapper、没有第二套实现的 adapter、从未用上的投机式间接层。把它们内联掉。
- **让相邻层提升抽象层次。** 重复相同方法与参数的层只增加 reader load、不压缩信息。折叠透传层。
- **要求接口压缩。** 面很广却几乎不藏复杂度的接口，会让读者同时学表面与实现。优先选择能遮住有意义决策的边界。
- **缩小状态作用域：** 优先纯函数（返回值而非 mutation）、局部优于字段、字段优于模块状态、模块状态优于 global。推导而非同步。
- **在边界处命名不变量，** 而非在每个消费者里各写一遍，让读者只学一次。
- 在加一层或一块状态之前自问：它是否在别处至少等量地降低了 reader load？

**The test:** 新读者能否在 30 秒内回答「X 从哪来？」和「什么能改变 X？」？若不能，砍掉层或砍掉状态。
