# `seatbelt.rs` — macOS Seatbelt 沙箱策略生成

## 功能概述

为 macOS 平台生成 Seatbelt (`sandbox-exec`) 沙箱策略和命令行参数。核心功能包括：(1) 根据文件系统和网络沙箱策略生成 SBPL (Seatbelt Profile Language) 策略文本；(2) 动态网络策略生成，支持代理端口白名单、本地回环绑定和 Unix 域套接字访问控制；(3) 文件读写策略生成，支持受保护子路径排除（如 `.git`、`.codex` 目录）；(4) 代理环境变量中的回环端口提取。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `MACOS_PATH_TO_SEATBELT_EXECUTABLE` | 常量 | sandbox-exec 路径固定为 `/usr/bin/sandbox-exec` 以防注入 |
| `ProxyPolicyInputs` | 结构体 (内部) | 代理策略输入：端口列表、是否有代理配置、是否允许本地绑定、Unix 套接字策略 |
| `UnixDomainSocketPolicy` | 枚举 (内部) | Unix 域套接字策略：AllowAll 或 Restricted（含允许列表） |
| `SeatbeltAccessRoot` | 结构体 (内部) | 访问根目录及其排除的子路径 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `create_seatbelt_command_args_for_policies` | `(command, file_system_policy, network_policy, cwd, enforce_managed_network, network) -> Vec<String>` | 生成完整的 sandbox-exec 命令行参数 |
| `create_seatbelt_command_args` | `(command, sandbox_policy, cwd, enforce_managed_network, network) -> Vec<String>` | 从旧版 SandboxPolicy 生成命令行参数 |
| `dynamic_network_policy_for_network` | `(network_policy, enforce_managed_network, proxy) -> String` | 生成动态网络策略 SBPL 片段 |
| `build_seatbelt_access_policy` | `(action, param_prefix, roots) -> (String, Vec<(String, PathBuf)>)` | 构建文件访问策略 SBPL 和参数定义 |
| `proxy_loopback_ports_from_env` | `(env) -> Vec<u16>` | 从代理环境变量中提取回环端口 |
| `unix_socket_policy` | `(proxy) -> String` | 生成 Unix 域套接字相关的 SBPL 策略 |

## 依赖关系

- **引用的 crate**: `codex_protocol`、`codex_network_proxy`、`codex_utils_absolute_path`、`url`
- **被引用方**: `manager.rs`（仅 macOS 编译）

## 实现备注

- 安全设计：强制使用 `/usr/bin/sandbox-exec` 而非 PATH 中的版本。
- 使用参数化策略（`-D` 参数）传递路径，避免在 SBPL 策略文本中硬编码路径。
- 文件保护使用 `require-all` + `require-not` 的 SBPL 构造来排除受保护的子路径。
- 排除子路径同时使用 `literal`（精确匹配）和 `subpath`（子路径匹配）确保目录创建和内容写入都被阻止。
- 网络策略在有代理配置时限制仅允许代理端口的回环流量，无代理时根据策略允许全部或禁止全部网络。
- 使用 `libc::confstr` 获取 macOS 系统缓存目录路径。
