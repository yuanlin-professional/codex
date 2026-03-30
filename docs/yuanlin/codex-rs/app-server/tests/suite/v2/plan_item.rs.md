# `plan_item.rs` — 测试模块

## 测试覆盖范围
测试计划模式（plan mode）下的计划项（plan item）通知行为，验证模型返回
proposed_plan 块时发送 plan item，以及没有 proposed_plan 时不发送。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `plan_mode_uses_proposed_plan_block_for_plan_item` | proposed_plan 块触发 plan item 通知 |
| `plan_mode_without_proposed_plan_does_not_emit_plan_item` | 无 proposed_plan 时不发送 plan item |
