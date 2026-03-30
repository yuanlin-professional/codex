# `fs.rs` — 测试模块

## 测试覆盖范围
测试文件系统操作 JSON-RPC 方法，包括 getMetadata、readFile、writeFile、createDirectory、
readDirectory、remove、copy、watch/unwatch，涵盖 base64 编码、路径验证、
递归复制、符号链接保留、特殊文件处理和文件变更通知。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `fs_get_metadata_returns_only_used_fields` | getMetadata 只返回所需字段 |
| `fs_methods_cover_current_fs_utils_surface` | 覆盖所有文件系统操作（读写删复制目录操作） |
| `fs_write_file_accepts_base64_bytes` | writeFile 支持 base64 编码的二进制数据 |
| `fs_write_file_rejects_invalid_base64` | writeFile 拒绝无效的 base64 数据 |
| `fs_methods_reject_relative_paths` | 所有方法拒绝相对路径 |
| `fs_copy_rejects_directory_without_recursive` | 复制目录时必须指定 recursive |
| `fs_copy_rejects_copying_directory_into_descendant` | 拒绝将目录复制到其子目录 |
| `fs_copy_preserves_symlinks_in_recursive_copy` | 递归复制保留符号链接 |
| `fs_copy_ignores_unknown_special_files_in_recursive_copy` | 递归复制跳过特殊文件 |
| `fs_copy_rejects_standalone_fifo_source` | 拒绝复制 FIFO 特殊文件 |
| `fs_watch_directory_reports_changed_child_paths_and_unwatch_stops_notifications` | watch 目录报告子路径变更，unwatch 停止通知 |
| `fs_watch_file_reports_atomic_replace_events` | watch 文件报告原子替换事件 |
| `fs_watch_allows_missing_file_targets` | watch 允许监视不存在的文件 |
| `fs_watch_rejects_relative_paths` | watch 拒绝相对路径 |
