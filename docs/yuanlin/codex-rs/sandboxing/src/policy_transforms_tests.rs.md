# `policy_transforms_tests.rs` — 测试模块

## 测试覆盖范围

测试沙箱策略转换与权限合并逻辑，覆盖平台沙箱判断、权限标准化、权限交集以及额外权限合并等场景。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `full_access_restricted_policy_skips_platform_sandbox_when_network_is_enabled` | 全盘写入 + 网络启用时不需要平台沙箱 |
| `root_write_policy_with_carveouts_still_uses_platform_sandbox` | 全盘写入但有排除路径时仍需平台沙箱 |
| `full_access_restricted_policy_still_uses_platform_sandbox_for_restricted_network` | 全盘写入 + 网络受限时需要平台沙箱 |
| `normalize_additional_permissions_preserves_network` | 标准化额外权限时保留网络和文件系统配置 |
| `normalize_additional_permissions_canonicalizes_symlinked_write_paths` | (仅 Unix) 标准化时符号链接路径被解析为规范路径 |
| `normalize_additional_permissions_drops_empty_nested_profiles` | 标准化时移除空的嵌套权限节 |
| `intersect_permission_profiles_preserves_explicit_empty_requested_reads` | 交集操作保留显式的空读取列表 |
| `intersect_permission_profiles_drops_ungranted_nonempty_path_requests` | 交集操作移除未授予的路径请求 |
| `intersect_permission_profiles_drops_explicit_empty_reads_without_grant` | 无授予时移除空读取列表 |
| `read_only_additional_permissions_can_enable_network_without_writes` | ReadOnly 策略的额外权限可以仅启用网络而不添加写入 |
| `external_sandbox_additional_permissions_can_enable_network` | ExternalSandbox 策略的额外权限可以启用网络 |
| `merge_file_system_policy_with_additional_permissions_preserves_unreadable_roots` | 合并额外权限时保留不可读根目录的排除条目 |
| `effective_file_system_sandbox_policy_returns_base_policy_without_additional_permissions` | 无额外权限时返回原始策略 |
| `effective_file_system_sandbox_policy_merges_additional_write_roots` | 合并额外的写入根目录到文件系统策略 |
