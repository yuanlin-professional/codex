# apply-patch/tests/fixtures/scenarios — 测试场景

## 功能概述
apply-patch 的各种测试场景目录。每个场景包含 input/、expected/ 和 patch.txt。

## 场景列表
| 场景 | 说明 |
|------|------|
| 001_add_file | 添加文件 |
| 002_multiple_operations | 多操作 |
| 003_multiple_chunks | 多代码块 |
| 004_move_to_new_directory | 移动到新目录 |
| 005_rejects_empty_patch | 拒绝空补丁 |
| 006_rejects_missing_context | 拒绝缺少上下文 |
| 007_rejects_missing_file_delete | 拒绝缺少文件删除 |
| 008_rejects_empty_update_hunk | 拒绝空更新块 |
| 009_requires_existing_file_for_update | 更新需要已有文件 |
| 010_move_overwrites_existing_destination | 移动覆盖目标 |
| 011_add_overwrites_existing_file | 添加覆盖已有文件 |
| 012_delete_directory_fails | 删除目录失败 |
| 013_rejects_invalid_hunk_header | 拒绝无效块头 |
| 014_update_file_appends_trailing_newline | 更新追加尾换行 |
| 015_failure_after_partial_success_leaves_changes | 部分成功后失败 |
| 016_pure_addition_update_chunk | 纯添加更新块 |
| 017_whitespace_padded_hunk_header | 空白填充块头 |
| 018_whitespace_padded_patch_markers | 空白填充补丁标记 |
| 019_unicode_simple | Unicode 简单补丁 |
| 020_delete_file_success | 删除文件成功 |
| 020_whitespace_padded_patch_marker_lines | 空白填充标记行 |
| 021_update_file_deletion_only | 仅删除更新 |
| 022_update_file_end_of_file_marker | 文件尾标记 |
