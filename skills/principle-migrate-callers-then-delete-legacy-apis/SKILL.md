---
name: principle-migrate-callers-then-delete-legacy-apis
description: "在引入新内部 API 而旧调用方仍存在时采用。在同一波迁移中迁完调用方并删除旧 API，而不是保留兼容层。"
disable-model-invocation: true
---

# Migrate Callers Then Delete Legacy APIs

- 更新测试以断言新契约，并删除仅保护重构前实现细节的测试

**When this applies:**
- 没有外部用户依赖向后兼容
- 项目能承受协调一致的破坏性变更
- 新 API 属于简化或重构举措的一部分
