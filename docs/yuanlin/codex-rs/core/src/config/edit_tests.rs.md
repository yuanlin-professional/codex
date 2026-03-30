# `edit_tests.rs` -- 测试模块

## 测试覆盖范围
覆盖 `edit.rs` 中配置编辑引擎的持久化逻辑，包括模型设置、MCP 服务器替换、skill 配置、通知标志、profile 作用域写入、inline table 保持、符号链接写穿透等。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `blocking_set_model_top_level` | 验证顶层模型和 reasoning effort 写入 |
| `builder_with_edits_applies_custom_paths` | 验证 Builder 的 `with_edits` 自定义路径写入 |
| `set_model_availability_nux_count_writes_shown_count` | 验证模型可用性 NUX 计数写入 |
| `set_skill_config_writes_disabled_entry` | 验证 skill 配置禁用条目写入 |
| `set_skill_config_removes_entry_when_enabled` | 验证启用 skill 时删除禁用条目 |
| `set_skill_config_writes_name_selector_entry` | 验证按名称选择器写入 skill 配置 |
| `blocking_set_model_preserves_inline_table_contents` | 验证 inline table 内容在模型设置后被保留 |
| `blocking_set_model_writes_through_symlink_chain` | 验证通过符号链接链写入目标文件 |
| `blocking_set_model_replaces_symlink_on_cycle` | 验证循环符号链接时替换为普通文件 |
| `batch_write_table_upsert_preserves_inline_comments` | 验证批量写入保留 inline 注释 |
| `blocking_clear_model_removes_inline_table_entry` | 验证清除模型时正确移除 inline table 条目 |
| `blocking_set_model_scopes_to_active_profile` | 验证模型设置自动作用域到活跃 profile |
| `blocking_set_model_with_explicit_profile` | 验证显式 profile 名称下的模型设置 |
| `blocking_set_hide_full_access_warning_preserves_table` | 验证设置 notice 标志时保留现有表内容 |
| `blocking_replace_mcp_servers_round_trips` | 验证 MCP 服务器配置的完整序列化与反序列化 |
| `blocking_replace_mcp_servers_serializes_tool_approval_overrides` | 验证 MCP 工具审批覆盖的序列化 |
| `blocking_replace_mcp_servers_preserves_inline_comments` | 验证替换 MCP 服务器时保留 inline 注释 |
| `blocking_replace_mcp_servers_preserves_inline_comment_suffix` | 验证保留行尾注释 |
| `blocking_replace_mcp_servers_preserves_inline_comment_after_removing_keys` | 验证删除键后保留注释 |
| `blocking_clear_path_noop_when_missing` | 验证清除不存在的路径不创建文件 |
| `blocking_set_path_updates_notifications` | 验证设置 path 更新通知配置 |
| `async_builder_set_model_persists` | 验证异步 Builder 模型持久化 |
| `blocking_builder_set_model_round_trips_back_and_forth` | 验证模型的反复设置和还原 |
| `blocking_builder_set_realtime_audio_persists_and_clears` | 验证实时音频设备设置和清除 |
| `replace_mcp_servers_blocking_clears_table_when_empty` | 验证空 MCP 服务器列表清除整个表 |
