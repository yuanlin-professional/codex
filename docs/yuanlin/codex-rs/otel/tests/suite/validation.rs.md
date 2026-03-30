# `validation.rs` — 测试模块

## 测试覆盖范围
该文件测试指标系统的输入校验逻辑，确保非法标签键/值、非法指标名称和负数计数器增量被正确拒绝。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `invalid_tag_component_is_rejected` | 含空格的标签键被拒绝 |
| `counter_rejects_invalid_tag_key` | 计数器调用时的非法标签键被拒绝 |
| `histogram_rejects_invalid_tag_value` | 直方图调用时的非法标签值被拒绝 |
| `counter_rejects_invalid_metric_name` | 含空格的指标名称被拒绝 |
| `counter_rejects_negative_increment` | 负数计数器增量被拒绝 |
