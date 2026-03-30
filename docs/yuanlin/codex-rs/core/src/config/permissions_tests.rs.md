# `permissions_tests.rs` -- 测试模块

## 测试覆盖范围
覆盖 `permissions.rs` 中的权限编译、路径规范化和网络配置叠加逻辑。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `normalize_absolute_path_for_platform_simplifies_windows_verbatim_paths` | 验证 Windows 设备路径 (`\\?\D:\...`) 被正确简化 |
| `restricted_read_implicitly_allows_helper_executables` | 验证受限模式自动允许读取 zsh 和 execve wrapper 路径，但不允许同级其他目录 |
| `network_toml_ignores_legacy_network_list_keys` | 验证旧版 `allowed_domains` 列表键被忽略 |
| `network_permission_containers_project_allowed_and_denied_entries` | 验证域名和 Unix socket 权限容器正确分离 allow/deny 条目 |
| `network_toml_overlays_unix_socket_permissions_by_path` | 验证 Unix socket 权限按路径叠加（后覆盖前） |
