---
name: typescript-best-practices
description: TypeScript 最佳实践。在阅读或编辑任何 .ts 或 .tsx 文件时使用。
paths: ["**/*.ts", "**/*.tsx"]
disable-model-invocation: true
---

# TypeScript best practices

先应用 **type-system-discipline** principle skill。

| Rule | Summary |
|------|---------|
| Discriminated unions | 用 `kind` 字面量 discriminant 建模变体，使不可能状态无法表示。不要 optional-field 大杂烩。 |
| Branded types | 用 `& { readonly __brand: "X" }` 给原始类型 branding，避免混用。在边界验证一次。brand 来自 verifying type guard（`input is AgentId`），不要来自 `as`。 |
| Constructive modeling | 构造型状使非法值无法构造：`[T, ...T[]]` 表非空，`[T, T][]` 表偶数长度，`start` 加 `duration` 表区间。不是 runtime guard，不是指望 refinement types。 |
| Simplest total type | 只要其上操作都 total 就保持 `T[]`。仅在宽松类型迫使你用 `!`、cast 或「不应发生」的 throw 时收紧为 `NonEmpty<T>`。 |
| `unknown` over `any` | 外部数据用 `unknown`。 |
| Schemas before guards | 手写逐属性 type guard 之前，用仓库的 runtime schema 库并从 schema 推断类型，如 `z.infer`。 |
| No `as` casts | 每个 `as` 都是待定时的 runtime crash。仅在验证后 cast。 |
| Narrowing hierarchy | discriminant switch > `in` > `typeof`/`instanceof` > 用户定义 type guard > `as`。 |
| Type guards | 必须验证主张。撒谎的 guard 比 `as` 更糟，因为 bug 躲在「安全」名字后面。命名为 `isX` 或 `hasX`。 |
| Exhaustiveness | 在 default 分支内联 `const _exhaustive: never = x;`，新增变体时编译器报错。 |
| `satisfies` over `as` | 校验值而不拓宽字面量类型。 |
| Boundary validation | 数据跨入时解析为命名 domain 类型。`Record<string, unknown>`（写法各异）止于该 parse。内部信任类型。见 **boundary-discipline** principle skill。 |
| Schema-derived types | 声明新 interface 之前先用 `Pick`/`Omit`/`Parameters`/`ReturnType`/`Awaited`/`typeof`。 |
| Object args | 传对象而非位置参数，自文档化参数顺序。热路径可跳过（每帧渲染、tokenizer、parser）。 |
| Real tests | 能跑的真实东西不要 mock。优先框架真实 test primitive 并做 leak/disposable 检查；在运行中的 build 里验证 UI。仅 mock 本地跑不了的东西。 |
| Structured telemetry | 优先结构化 logger 诊断，上下文足以用 id 调试。已交付代码里不要 `console.log`。 |

示例：`references/patterns.md`。
