# `file_system.rs` — 测试模块

## 测试覆盖范围

通过 `ExecutorFileSystem` trait 接口测试本地和远程文件系统操作，使用 `test_case` 参数化测试。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `file_system_get_metadata_returns_expected_fields` | 文件元数据包含正确的类型和时间戳 |
| `file_system_methods_cover_surface_area` | 完整覆盖 mkdir/write/read/copy/readdir/remove |
| `file_system_copy_rejects_directory_without_recursive` | 非递归复制目录返回 InvalidInput |
| `file_system_copy_rejects_copying_directory_into_descendant` | 复制目录到子目录被拒绝 |
| `file_system_copy_preserves_symlinks_in_recursive_copy` | 递归复制保留符号链接 |
| `file_system_copy_ignores_unknown_special_files_in_recursive_copy` | 递归复制跳过 FIFO 等特殊文件 |
| `file_system_copy_rejects_standalone_fifo_source` | 单独复制 FIFO 文件被拒绝 |
