# `shell_serialization.rs` — 测试模块

## 测试覆盖范围

Shell 命令和 apply_patch 工具的输出序列化格式测试。覆盖三种 shell 输出模式（`Shell`、`ShellCommand`、`LocalShell`）以及四种 apply_patch 输出模式（`Freeform`、`Function`、`Shell`、`ShellViaHeredoc`）下的结构化输出格式、JSON 保留、截断行为和持续时间记录。仅在非 Windows 平台运行。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `shell_output_stays_json_without_freeform_apply_patch` | 未启用 freeform apply_patch 时，shell 输出保持 JSON 格式（含 metadata.exit_code 和 output 字段） |
| `shell_output_is_structured_with_freeform_apply_patch` | 启用 freeform apply_patch 后，shell 输出切换为 "Exit code / Wall time / Output" 纯文本结构化格式 |
| `shell_output_preserves_fixture_json_without_serialization` | 未启用 apply_patch 时，JSON fixture 文件内容在 output 字段中完整保留 |
| `shell_output_structures_fixture_with_serialization` | 启用 apply_patch 后，JSON fixture 内容嵌入结构化纯文本的 Output 段落中 |
| `shell_output_for_freeform_tool_records_duration` | 结构化输出中的 "Wall time" 字段正确反映命令实际执行耗时 |
| `shell_output_reserializes_truncated_content` | 当设置 `tool_output_token_limit` 时，大输出被正确截断并标注 "tokens truncated" |
| `apply_patch_custom_tool_output_is_structured` | apply_patch 自定义工具输出为结构化格式，包含 "Success" 和文件变更列表 |
| `apply_patch_custom_tool_call_creates_file` | apply_patch 调用成功创建新文件并返回结构化成功输出 |
| `apply_patch_custom_tool_call_updates_existing_file` | apply_patch 调用成功更新已有文件 |
| `apply_patch_custom_tool_call_reports_failure_output` | apply_patch 对不存在文件的更新操作返回验证失败信息 |
| `apply_patch_function_call_output_is_structured` | 以 function call 方式调用 apply_patch 时输出同样是结构化格式 |
| `shell_output_is_structured_for_nonzero_exit` | 非零退出码的命令输出也保持结构化格式 |
| `shell_command_output_is_freeform` | `shell_command` 工具输出始终为结构化纯文本格式 |
| `shell_command_output_is_not_truncated_under_10k_bytes` | 输出不超过 10000 字节时不截断 |
| `shell_command_output_is_not_truncated_over_10k_bytes` | 输出超过 10000 字节时执行字符级截断 |
| `local_shell_call_output_is_structured` | `local_shell_call` 响应类型的输出同样为结构化格式 |
