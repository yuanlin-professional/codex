# `web_search.rs` -- 测试模块

## 测试覆盖范围
测试 web_search 工具的模式配置和请求参数生成，包括 Cached 模式下 external_web_access 的设置、模式优先级与遗留标志的关系、默认模式行为、跨 turn 的沙箱策略切换，以及 config.toml 中高级配置（context_size、allowed_domains、location）的传递。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `web_search_mode_cached_sets_external_web_access_false` | Cached 模式下 web_search 工具的 external_web_access=false |
| `web_search_mode_takes_precedence_over_legacy_flags` | web_search_mode 优先于遗留的 WebSearchRequest feature flag |
| `web_search_mode_defaults_to_cached_when_features_disabled` | 禁用 WebSearchCached/WebSearchRequest feature 后默认为 cached 模式 |
| `web_search_mode_updates_between_turns_with_sandbox_policy` | ReadOnly 策略下为 cached（external_web_access=false），DangerFullAccess 下为 live（external_web_access=true） |
| `web_search_tool_config_from_config_toml_is_forwarded_to_request` | config.toml 中的 context_size、allowed_domains、location 被完整传递到请求中 |
