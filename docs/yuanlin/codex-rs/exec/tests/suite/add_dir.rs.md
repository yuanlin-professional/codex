# `add_dir.rs` — 测试模块

## 测试覆盖范围

验证 `--add-dir` CLI 标志是否被正确接受和处理。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `accepts_add_dir_flag` | 使用 --add-dir 和 workspace-write 沙箱成功运行 |
| `accepts_multiple_add_dir_flags` | 多个 --add-dir 标志可以同时指定 |
