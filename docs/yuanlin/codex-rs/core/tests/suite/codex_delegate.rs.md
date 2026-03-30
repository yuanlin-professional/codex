# `codex_delegate.rs` — 测试模块

## 测试覆盖范围

测试 Codex 委托(Delegate)机制，包括子代理审批请求的转发与处理（exec 审批和 apply_patch 审批），以及推理摘要 delta 事件的正确处理。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `codex_delegate_forwards_exec_approval_and_proceeds_on_approval` | (暂跳过) 委托转发子代理的 exec 审批请求，父级批准后继续执行 |
| `codex_delegate_forwards_patch_approval_and_proceeds_on_decision` | (暂跳过) 委托转发子代理的 apply_patch 审批请求，父级拒绝后继续执行 |
| `codex_delegate_ignores_legacy_deltas` | 委托正确处理推理摘要 delta 事件，验证新旧两种格式各产生一个 delta |
