# `windows_sandbox_read_grants.rs` — Windows 沙箱只读目录授权

该文件提供 `grant_read_root_non_elevated` 函数，用于在 Windows 沙箱（非提权模式）下为指定目录授予只读访问权限。函数首先验证路径必须是绝对路径、已存在且为目录，然后通过 `dunce::canonicalize` 获取规范化路径，最后调用 `run_setup_refresh_with_extra_read_roots` 刷新沙箱配置以包含新的只读根目录。返回规范化后的路径。对应的测试位于 `windows_sandbox_read_grants_tests.rs` 文件中。
