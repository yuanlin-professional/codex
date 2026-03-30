# `deprecation_notice.rs` — 测试模块

## 测试覆盖范围

测试弃用通知(Deprecation Notice)事件的发出，包括旧版 feature flag 名称、已弃用的 experimental_instructions_file 配置，以及已弃用的 web_search_request feature flag 值。仅在非 Windows 平台运行。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `emits_deprecation_notice_for_legacy_feature_flag` | 使用旧版 feature flag (use_experimental_unified_exec_tool) 时发出弃用通知，建议使用 unified_exec |
| `emits_deprecation_notice_for_experimental_instructions_file` | 使用已弃用的 experimental_instructions_file 配置时发出弃用通知，建议使用 model_instructions_file |
| `emits_deprecation_notice_for_web_search_feature_flag_values` | 使用已弃用的 web_search_request feature flag (true/false) 时发出弃用通知，建议使用 web_search 顶层配置 |
