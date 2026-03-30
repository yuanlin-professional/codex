# `thread_metadata_update.rs` — 测试模块

## 测试覆盖范围
测试 `thread/metadata/update` JSON-RPC 方法，验证 git 分支补丁更新、空补丁拒绝、
缺失 SQLite 行修复（存储线程/归档线程）、已加载线程修复保持摘要不变，以及清除 git 字段。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `thread_metadata_update_patches_git_branch_and_returns_updated_thread` | 补丁更新 git 分支并返回更新后的线程 |
| `thread_metadata_update_rejects_empty_git_info_patch` | 空 git 信息补丁被拒绝 |
| `thread_metadata_update_repairs_missing_sqlite_row_for_stored_thread` | 修复存储线程缺失的 SQLite 行 |
| `thread_metadata_update_repairs_loaded_thread_without_resetting_summary` | 修复已加载线程但不重置摘要 |
| `thread_metadata_update_repairs_missing_sqlite_row_for_archived_thread` | 修复归档线程缺失的 SQLite 行 |
| `thread_metadata_update_can_clear_stored_git_fields` | 可以清除已存储的 git 字段 |
