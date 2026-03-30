# `session_tests.rs` — 测试模块

## 测试覆盖范围

针对 `SessionState` 的连接器选择管理和速率限制合并逻辑进行测试，包括去重、清除、默认 limit_id 填充以及跨快照的 credits/plan_type 继承行为。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `merge_connector_selection_deduplicates_entries` | 验证 `merge_connector_selection` 对重复的连接器 ID 进行去重 |
| `clear_connector_selection_removes_entries` | 验证 `clear_connector_selection` 清除所有已保存的连接器 ID |
| `set_rate_limits_defaults_limit_id_to_codex_when_missing` | 验证当速率限制快照缺少 `limit_id` 时默认填充为 `"codex"` |
| `set_rate_limits_defaults_to_codex_when_limit_id_missing_after_other_bucket` | 验证在先有 `codex_other` 桶后，新快照缺少 `limit_id` 时仍默认为 `"codex"` |
| `set_rate_limits_carries_credits_and_plan_type_from_codex_to_codex_other` | 验证当新快照缺少 `credits` 和 `plan_type` 时，从前一快照继承这些字段 |
