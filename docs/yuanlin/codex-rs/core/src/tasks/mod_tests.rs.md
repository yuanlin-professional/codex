# `mod_tests.rs` — 测试模块

## 测试覆盖范围

测试 `tasks/mod.rs` 中 `emit_turn_network_proxy_metric` 函数的遥测指标发射行为，使用内存中的指标导出器验证计数器值和属性标签。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `emit_turn_network_proxy_metric_records_active_turn` | 验证网络代理活跃时，计数器值为 1 且 `active` 属性为 `"true"` |
| `emit_turn_network_proxy_metric_records_inactive_turn` | 验证网络代理非活跃时，计数器值为 1 且 `active` 属性为 `"false"` |
