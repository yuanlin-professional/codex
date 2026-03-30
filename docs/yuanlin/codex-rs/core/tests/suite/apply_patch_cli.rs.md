# `apply_patch_cli.rs` — 测试模块

## 测试覆盖范围

测试 apply_patch CLI 工具的集成功能，覆盖文件的添加、修改、删除、移动操作，以及错误处理（无效 hunk 头、上下文缺失、路径穿越）、diff 事件生成、多调用聚合等场景。每个场景均通过多种模型输出格式(Freeform/Function/Shell/ShellViaHeredoc/ShellCommandViaHeredoc)参数化测试。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `apply_patch_cli_uses_codex_self_exe_with_linux_sandbox_helper_alias` | (仅 Linux) 验证 apply_patch 使用 codex 自身可执行文件作为沙箱辅助别名 |
| `apply_patch_cli_multiple_operations_integration` | 单个补丁中同时执行添加、删除、修改操作并验证文件系统结果 |
| `apply_patch_cli_multiple_chunks` | 对单个文件应用包含多个 hunk 的补丁，验证多处修改均正确应用 |
| `apply_patch_cli_moves_file_to_new_directory` | 将文件移动到新目录并修改内容，验证源文件删除和目标文件创建 |
| `apply_patch_cli_updates_file_appends_trailing_newline` | 更新不以换行结尾的文件时，自动追加尾部换行符 |
| `apply_patch_cli_insert_only_hunk_modifies_file` | 仅插入行的 hunk 正确修改文件 |
| `apply_patch_cli_move_overwrites_existing_destination` | 移动文件时覆盖已存在的目标文件 |
| `apply_patch_cli_move_without_content_change_has_no_turn_diff` | 纯重命名(无内容变更)不应产生 TurnDiff 事件 |
| `apply_patch_cli_add_overwrites_existing_file` | Add File 操作覆盖已存在的同名文件 |
| `apply_patch_cli_rejects_invalid_hunk_header` | 拒绝包含无效 hunk 头的补丁并报告验证失败 |
| `apply_patch_cli_reports_missing_context` | 更新文件时上下文行不匹配，报告验证失败且文件不被修改 |
| `apply_patch_cli_reports_missing_target_file` | 尝试更新不存在的文件，报告验证失败 |
| `apply_patch_cli_delete_missing_file_reports_error` | 尝试删除不存在的文件，报告验证失败 |
| `apply_patch_cli_rejects_empty_patch` | 拒绝空补丁并返回相应错误信息 |
| `apply_patch_cli_delete_directory_reports_verification_error` | 尝试删除目录(非文件)，报告验证失败 |
| `apply_patch_cli_rejects_path_traversal_outside_workspace` | 拒绝路径穿越到工作区外部的补丁 |
| `apply_patch_cli_rejects_move_path_traversal_outside_workspace` | 拒绝移动目标路径穿越到工作区外部的补丁 |
| `apply_patch_cli_verification_failure_has_no_side_effects` | 验证失败时不应产生任何文件系统副作用（事务性保证） |
| `apply_patch_shell_command_heredoc_with_cd_updates_relative_workdir` | 通过 shell heredoc 并使用 cd 切换目录后应用补丁 |
| `apply_patch_cli_can_use_shell_command_output_as_patch_input` | 将 shell 命令输出作为补丁输入，支持 UTF-8 内容 |
| `apply_patch_shell_command_heredoc_with_cd_emits_turn_diff` | shell heredoc 方式应用补丁后正确产生 TurnDiff 和 PatchApply 事件 |
| `apply_patch_shell_command_failure_propagates_error_and_skips_diff` | shell 方式验证失败时传播错误且不产生 TurnDiff |
| `apply_patch_function_accepts_lenient_heredoc_wrapped_patch` | 接受宽松格式的 heredoc 包装补丁 |
| `apply_patch_cli_end_of_file_anchor` | 支持 `*** End of File` 文件末尾锚点 |
| `apply_patch_cli_missing_second_chunk_context_rejected` | 第二个 chunk 缺少 @@ 标记时拒绝补丁 |
| `apply_patch_emits_turn_diff_event_with_unified_diff` | 成功应用补丁后产生包含 unified diff 的 TurnDiff 事件 |
| `apply_patch_turn_diff_for_rename_with_content_change` | 重命名+内容修改后 TurnDiff 包含新旧路径和内容变更 |
| `apply_patch_aggregates_diff_across_multiple_tool_calls` | 跨多次工具调用聚合 diff |
| `apply_patch_aggregates_diff_preserves_success_after_failure` | 部分失败后仍保留成功操作的 diff |
| `apply_patch_change_context_disambiguates_target` | 使用变更上下文(@@后的锚文本)消除多处匹配的歧义 |
