---
name: principle-boundary-discipline
description: "在接 validation、错误处理或框架 adapter 时适用。把 guard 集中在系统边界（CLI、config、网络、外部 API）；内部信任类型，业务逻辑放在纯函数里。"
disable-model-invocation: true
---

# Boundary Discipline

把 validation、类型收窄与错误处理放在系统边界。无条件信任内部代码。业务逻辑写在纯函数里。外壳薄且机械。

**原因：** 分散的 validation 嘈杂、冗余，并给人虚假安全感。把逻辑移出框架接线，才能在不搭框架的情况下测试。

**模式：**
- **在边界**（CLI 参数、config 文件、外部 API、网络协议）：验证、返回错误、防御性处理。
- **系统内部：** 类型化数据、错误传播、不再验证。信任类型。
- **跨边界。** 暴露领域概念，而非边界的私有表示。通用机制在内，专用策略在边缘。

**应用：**

验证与错误处理：
- 在解析时（边界）验证 config，不在业务逻辑里
- 在边界把原始数据解析成领域类型
- 不要经公共面再导出 transport、storage、framework 或 wire 类型
- 若边界已验证，调用链深处不要 redundant nil 检查

代码组织：
- 业务逻辑放在无框架依赖的纯函数里
- 解析函数：从原始字节到类型化状态的纯变换
- Prompt 构造：结构化状态入、字符串出
- 评分与评估：从状态到结果的纯变换

**检验：**
- 「这些数据此刻是否正跨越系统边界？」若不是，validation 是冗余的。
- 「这能否做成外壳只调用的纯函数？」若能，抽出来。
