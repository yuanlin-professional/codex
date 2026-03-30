# `path_utils_tests.rs` — 测试模块

## 测试覆盖范围

测试 `path-utils` 的符号链接解析、WSL 路径规范化和 Windows 原生工作目录路径处理。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `symlink_cycles_fall_back_to_root_write_path` | 符号链接循环时回退到原始路径 |
| `wsl_mnt_drive_paths_lowercase` | WSL 下 `/mnt/C/Users/Dev` 被小写化 |
| `wsl_non_drive_paths_unchanged` | WSL 下非单字母驱动路径不变 |
| `wsl_non_mnt_paths_unchanged` | WSL 下非 `/mnt` 路径不变 |
| `windows_verbatim_paths_are_simplified` | Windows verbatim 路径被简化（仅 Windows） |
| `non_windows_paths_are_unchanged` | 非 Windows 模式下路径不变 |
