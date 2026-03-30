# `manager_metrics.rs` — 测试模块

## 测试覆盖范围
该文件测试 `SessionTelemetry` 在转发指标时的元数据标签附加功能，包括启用/禁用元数据标签以及可选 service_name 标签的行为。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `manager_attaches_metadata_tags_to_metrics` | SessionTelemetry 正确附加 model/auth_mode/originator 等元数据标签 |
| `manager_allows_disabling_metadata_tags` | 可禁用元数据标签仅保留调用时标签 |
| `manager_attaches_optional_service_name_tag` | 可附加可选的 service_name 标签 |
