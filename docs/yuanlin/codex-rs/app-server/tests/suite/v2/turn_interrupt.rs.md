# `turn_interrupt.rs` — 测试模块

## 测试覆盖范围
测试 `turn/interrupt` JSON-RPC 方法，验证中断运行中的 turn 以及解决待处理的命令审批请求。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `turn_interrupt_aborts_running_turn` | 中断运行中的 turn 使其终止 |
| `turn_interrupt_resolves_pending_command_approval_request` | 中断解决待处理的命令审批请求 |
