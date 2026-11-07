---
name: principle-type-system-discipline
description: "在设计类型、审阅函数签名或在任何静态类型语言中写代码时采用。让非法状态无法表示、为语义原语加 brand、在边界解析外部数据、穷尽 variant、从权威 schema 推导。"
disable-model-invocation: true
---

# Type System Discipline

让编译器拒绝不可能的状态、不匹配的原语以及未处理的 variant。优先通过定义把错误与特例排除在表示之外，而不是泛滥 handler。TypeScript 语法见 `typescript-best-practices` skill。

- **Brand 语义原语。** `UserId` 与 `OrderId` 底层都是 string，但不应互换。各给独立类型，例如在 TypeScript 中用 branded intersection，在其他语言中用 newtype。创建时校验一次，下游信任类型。若两个参数共享同一原语类型但语义不同，给它们 brand。
- **外部数据在解析前视为无类型。** RPC payload、JSON、IPC 消息、CLI 参数、配置文件、环境变量、数据库行。在每个边界用 parse 函数把非结构化输入转成类型化模型。**boundary-discipline** principle skill 说明校验应放在哪。
- **从权威 schema 推导类型。** 当 protocol buffer、OpenAPI spec、GraphQL schema、数据库 migration、design-token 文件或其他文件拥有形状时，从其推导，不要手写平行类型。见 **encode-lessons-in-structure** principle skill。
