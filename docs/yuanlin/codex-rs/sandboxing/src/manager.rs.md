# `manager.rs` — 沙箱管理器：选择与转换

## 功能概述

`SandboxManager` 是沙箱执行的核心编排组件，负责两大功能：(1) 根据文件系统策略、网络策略和用户偏好选择合适的沙箱类型；(2) 将通用的 `SandboxCommand` 转换为平台特定的 `SandboxExecRequest`，包括构建 macOS Seatbelt 或 Linux Seccomp 的命令行参数，合并额外权限配置等。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `SandboxType` | 枚举 | 沙箱类型：None、MacosSeatbelt、LinuxSeccomp、WindowsRestrictedToken |
| `SandboxablePreference` | 枚举 | 沙箱偏好：Auto（自动）、Require（强制）、Forbid（禁用） |
| `SandboxCommand` | 结构体 | 待沙箱化的命令，包含 program、args、cwd、env 和可选的额外权限 |
| `SandboxExecRequest` | 结构体 | 转换后的沙箱执行请求，包含最终命令、所有策略和沙箱配置 |
| `SandboxTransformRequest` | 结构体 | 转换请求的参数包，包含命令、策略、沙箱类型、网络配置等 |
| `SandboxTransformError` | 枚举 | 转换错误：缺少 Linux 沙箱可执行文件或 Seatbelt 不可用 |
| `SandboxManager` | 结构体 | 沙箱管理器（无状态单例） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `get_platform_sandbox` | `(windows_sandbox_enabled) -> Option<SandboxType>` | 返回当前平台的可用沙箱类型 |
| `SandboxManager::select_initial` | `(&self, file_system_policy, network_policy, pref, ...) -> SandboxType` | 根据策略和偏好选择初始沙箱类型 |
| `SandboxManager::transform` | `(&self, request) -> Result<SandboxExecRequest, SandboxTransformError>` | 将通用命令转换为平台特定的沙箱执行请求 |

## 依赖关系

- **引用的 crate**: `codex_protocol`、`codex_network_proxy`
- **被引用方**: `lib.rs`（公开导出），被 Codex runtime 层使用

## 实现备注

- `select_initial` 在 Auto 模式下根据 `should_require_platform_sandbox` 判断是否需要平台沙箱。
- `transform` 方法处理额外权限的合并（通过 `EffectiveSandboxPermissions`），然后根据沙箱类型分发到对应的平台实现。
- Linux Seccomp 模式下，通过 `linux_sandbox_arg0_override` 设置 argv[0] 为 `codex-linux-sandbox`。
