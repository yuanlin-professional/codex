# `remote_models.rs` -- 测试模块

## 测试覆盖范围
大型测试文件，覆盖远程模型管理器（ModelsManager）的各项功能，包括模型信息获取（最长前缀匹配）、长 slug 模型的推理参数传递、命名空间模型、unified exec 工具类型、truncation 策略、远程 base instructions、模型列表合并逻辑（新增/替换/空响应保留/隐藏模型）以及请求超时处理。仅在非 Windows 系统上运行。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `remote_models_get_model_info_uses_longest_matching_prefix` | 请求 "gpt-5.3-codex-test" 时匹配 "gpt-5.3-codex" 的元数据而非 "gpt-5.3" |
| `remote_models_long_model_slug_is_sent_with_high_reasoning` | 长 slug 模型请求中携带正确的 reasoning effort 和 summary |
| `namespaced_model_slug_uses_catalog_metadata_without_fallback_warning` | "custom/gpt-5.2-codex" 格式的命名空间模型不触发 fallback 警告 |
| `remote_models_remote_model_uses_unified_exec` | 远程模型配置为 UnifiedExec shell type 时，使用 exec_command 工具 |
| `remote_models_truncation_policy_without_override_preserves_remote` | 远程模型的 truncation 策略在无本地覆盖时被保留 |
| `remote_models_truncation_policy_with_tool_output_override` | 本地 tool_output_token_limit 覆盖远程 truncation 策略 |
| `remote_models_apply_remote_base_instructions` | 远程模型使用基础模型的 base instructions |
| `remote_models_do_not_append_removed_builtin_presets` | 远程模型不追加被删除的内置预设 |
| `remote_models_merge_adds_new_high_priority_first` | 高优先级远程模型排在列表首位 |
| `remote_models_merge_replaces_overlapping_model` | 与内置模型 slug 相同的远程模型覆盖内置模型信息 |
| `remote_models_merge_preserves_bundled_models_on_empty_response` | 空远程响应不影响内置模型列表 |
| `remote_models_request_times_out_after_5s` | /models 请求超时 5 秒后返回默认模型 |
| `remote_models_hide_picker_only_models` | visibility=Hide 的远程模型不在 picker 中显示 |
