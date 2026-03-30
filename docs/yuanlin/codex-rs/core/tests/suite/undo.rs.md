# `undo.rs` -- 测试模块

## 测试覆盖范围
测试 undo（撤销）功能的各种场景，基于 ghost commit 快照机制。包括新建文件撤销、已跟踪/未跟踪文件编辑恢复、多 turn 逐次撤销、不影响无关文件、快照消耗、无快照时报错、文件移动/重命名恢复、gitignore 目录保护、覆盖手动编辑，以及保留无关的暂存变更。仅在非 Windows 系统上运行。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `undo_removes_new_file_created_during_turn` | 撤销后新建的文件被删除 |
| `undo_restores_tracked_file_edit` | 撤销后已跟踪文件恢复到编辑前内容，git status 干净 |
| `undo_restores_untracked_file_edit` | 撤销后未跟踪文件恢复到编辑前内容 |
| `undo_reverts_only_latest_turn` | 多 turn 后撤销仅恢复到上一 turn 的状态 |
| `undo_does_not_touch_unrelated_files` | 撤销不影响未被 turn 修改的文件（tracked、untracked、ignored） |
| `undo_sequential_turns_consumes_snapshots` | 3 个 turn 逐次撤销后快照耗尽，第 4 次撤销失败 |
| `undo_without_snapshot_reports_failure` | 无 ghost 快照时撤销返回 "No ghost snapshot available to undo." |
| `undo_restores_moves_and_renames` | 撤销文件移动/重命名操作，原文件恢复，目标文件删除 |
| `undo_does_not_touch_ignored_directory_contents` | gitignore 目录中的文件不受撤销影响 |
| `undo_overwrites_manual_edits_after_turn` | turn 后的手动编辑被撤销覆盖，恢复到 turn 前状态 |
| `undo_preserves_unrelated_staged_changes` | 撤销不影响用户暂存的无关文件变更 |
