# `cli.rs` -- 测试模块

## 测试覆盖范围

通过 assert_cmd 对 apply_patch 的 CLI 二进制程序进行端到端集成测试，验证各种 patch 操作的成功/失败行为、stdout/stderr 输出以及文件系统最终状态。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `test_apply_patch_cli_applies_multiple_operations` | 同时添加文件、删除文件和更新文件，验证多操作 patch |
| `test_apply_patch_cli_applies_multiple_chunks` | 单文件多 chunk 更新，验证多个 @@ 区块的正确应用 |
| `test_apply_patch_cli_moves_file_to_new_directory` | 文件移动+内容更新，验证 Move to 指令 |
| `test_apply_patch_cli_rejects_empty_patch` | 空 patch（无任何 hunk）应返回失败 |
| `test_apply_patch_cli_reports_missing_context` | 更新 hunk 的上下文行不匹配时应报告错误 |
| `test_apply_patch_cli_rejects_missing_file_delete` | 删除不存在的文件应返回失败 |
| `test_apply_patch_cli_rejects_empty_update_hunk` | Update 指令没有 chunk 时应报错 |
| `test_apply_patch_cli_requires_existing_file_for_update` | 更新不存在的文件应报错 |
| `test_apply_patch_cli_move_overwrites_existing_destination` | Move 操作覆盖已存在的目标文件 |
| `test_apply_patch_cli_add_overwrites_existing_file` | Add 操作覆盖已存在的同名文件 |
| `test_apply_patch_cli_delete_directory_fails` | 尝试删除目录（非文件）应返回失败 |
| `test_apply_patch_cli_rejects_invalid_hunk_header` | 无效的 hunk 头（如 Frobnicate）应报错 |
| `test_apply_patch_cli_updates_file_appends_trailing_newline` | 更新操作确保文件以换行符结尾 |
| `test_apply_patch_cli_failure_after_partial_success_leaves_changes` | 部分成功后遇到失败，已成功的变更保留不回滚 |
