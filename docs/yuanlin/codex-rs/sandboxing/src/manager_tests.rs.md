# `manager_tests.rs` — 测试模块

## 测试覆盖范围

测试沙箱管理器的初始选择逻辑和命令转换功能，覆盖各种策略组合和额外权限场景。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `danger_full_access_defaults_to_no_sandbox_without_network_requirements` | 完全访问模式无网络要求时默认不使用沙箱 |
| `danger_full_access_uses_platform_sandbox_with_network_requirements` | 完全访问模式有网络要求时使用平台沙箱 |
| `restricted_file_system_uses_platform_sandbox_without_managed_network` | 受限文件系统策略即使无托管网络也使用平台沙箱 |
| `transform_preserves_unrestricted_file_system_policy_for_restricted_network` | 转换时保留无限制文件系统策略和受限网络策略 |
| `transform_additional_permissions_enable_network_for_external_sandbox` | 额外权限可以为外部沙箱启用网络 |
| `transform_additional_permissions_preserves_denied_entries` | 额外权限合并时保留拒绝条目 |
| `transform_linux_seccomp_preserves_helper_path_in_arg0_when_available` | (仅 Linux) 当辅助程序路径为 codex-linux-sandbox 时保留完整路径 |
| `transform_linux_seccomp_uses_helper_alias_when_launcher_is_not_helper_path` | (仅 Linux) 当辅助程序路径不是 codex-linux-sandbox 时使用别名 |
