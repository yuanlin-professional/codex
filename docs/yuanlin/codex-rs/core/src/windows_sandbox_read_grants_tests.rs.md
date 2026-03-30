# `windows_sandbox_read_grants_tests.rs` — 测试模块

## 测试覆盖范围

测试 Windows 沙箱环境下非提升权限的只读路径授权函数 `grant_read_root_non_elevated` 的输入校验逻辑，验证对无效路径参数的拒绝行为。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `rejects_relative_path` | 传入相对路径时应返回错误，错误信息包含 "path must be absolute" |
| `rejects_missing_path` | 传入不存在的路径时应返回错误，错误信息包含 "path does not exist" |
| `rejects_file_path` | 传入文件路径（非目录）时应返回错误，错误信息包含 "path must be a directory" |
