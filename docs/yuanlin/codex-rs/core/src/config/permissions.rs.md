# `permissions.rs` -- 权限配置定义与编译

## 功能概述
定义了 Codex 配置中的权限体系类型（文件系统权限、网络权限）和编译逻辑。将 TOML 格式的权限配置（`PermissionsToml`）编译为运行时可用的 `FileSystemSandboxPolicy` 和 `NetworkSandboxPolicy`。支持特殊路径（如 `:root`、`:project_roots`、`:tmpdir`）、作用域嵌套、Windows 设备路径规范化等。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `PermissionsToml` | struct | 权限配置的 TOML 顶层结构，包含按名称索引的 profile 映射 |
| `PermissionProfileToml` | struct | 单个权限 profile，包含 `filesystem` 和 `network` 两部分 |
| `FilesystemPermissionsToml` | struct | 文件系统权限条目映射 |
| `FilesystemPermissionToml` | enum | 文件系统权限，`Access`（简单模式）或 `Scoped`（嵌套子路径模式） |
| `NetworkDomainPermissionsToml` | struct | 网络域名权限条目映射 |
| `NetworkDomainPermissionToml` | enum | 域名权限：`Allow` 或 `Deny` |
| `NetworkUnixSocketPermissionsToml` | struct | Unix socket 权限条目映射 |
| `NetworkToml` | struct | 网络配置的完整 TOML 结构，包含 proxy、SOCKS5、域名、socket 等设置 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `compile_permission_profile` | `fn(permissions, profile_name, warnings) -> Result<(FSSandboxPolicy, NetSandboxPolicy)>` | 将权限 profile 编译为运行时策略 |
| `resolve_permission_profile` | `fn(permissions, profile_name) -> Result<&PermissionProfileToml>` | 按名称解析权限 profile |
| `NetworkToml::apply_to_network_proxy_config` | `fn(&self, config)` | 将网络 TOML 配置应用到代理配置 |
| `overlay_network_domain_permissions` | `fn(config, domains)` | 叠加域名权限到网络代理配置 |
| `get_readable_roots_required_for_codex_runtime` | `fn(codex_home, zsh_path, wrapper_exe) -> Vec<AbsolutePathBuf>` | 返回 Codex 运行必需的可读路径列表 |
| `parse_special_path` | `fn(path) -> Option<FileSystemSpecialPath>` | 解析以 `:` 开头的特殊路径标识符 |
| `parse_absolute_path_for_platform` | `fn(path, is_windows) -> Result<AbsolutePathBuf>` | 跨平台的绝对路径解析，支持 Windows 设备路径 |

## 依赖关系
- **引用的 crate**: `codex_network_proxy`, `codex_protocol`, `codex_utils_absolute_path`, `schemars`, `serde`
- **被引用方**: `config/mod.rs` 中编译权限策略时使用

## 实现备注
- 特殊路径解析是前向兼容的：未知的 `:xxx` 路径通过 `FileSystemSpecialPath::Unknown` 保留并以警告形式上报，而非拒绝配置加载。
- `normalize_windows_device_path` 处理 `\\?\`、`\\.\`、UNC 等 Windows 设备路径前缀。
- `compile_scoped_filesystem_path` 支持嵌套路径，例如 `:project_roots` 下的子路径。
