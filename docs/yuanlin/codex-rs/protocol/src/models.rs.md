# `models.rs` — 核心数据模型与业务逻辑类型

## 功能概述

定义了 Codex 核心运行时的主要数据模型，包括沙箱权限控制、文件系统权限、网络权限、模型配置参数、内容项、响应项、Web 搜索动作等。是 protocol crate 中最大的文件之一，承载了从 Agent 配置、请求构造到沙箱策略生成的大量业务逻辑。模型元数据（如 `BaseInstructions`、`ContentItem`、`ResponseItem` 等）也在此定义。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `SandboxPermissions` | 枚举 | 每命令沙箱覆盖策略（UseDefault / RequireEscalated / WithAdditionalPermissions） |
| `FileSystemPermissions` | 结构体 | 文件系统读写路径权限列表 |
| `NetworkPermissions` | 结构体 | 网络是否启用的权限标志 |
| `PermissionProfile` | 结构体 | 文件系统权限 + 网络权限的组合配置 |
| `ContentItem` | 枚举 | 消息内容项（InputText 等） |
| `ResponseItem` | 枚举 | API 响应项（Message 等） |
| `MessagePhase` | 枚举 | 消息阶段标识 |
| `WebSearchAction` | 结构体/枚举 | 网页搜索动作 |
| `BaseInstructions` | 类型 | 基础指令模板 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `SandboxPermissions::requires_escalated_permissions` | `fn requires_escalated_permissions(self) -> bool` | 判断是否需要升级权限 |
| `SandboxPermissions::requests_sandbox_override` | `fn requests_sandbox_override(self) -> bool` | 判断是否请求沙箱覆盖 |

## 依赖关系

- **引用的 crate**: `codex_utils_image`, `codex_utils_template`, `codex_execpolicy`, `codex_git_utils`, `codex_utils_absolute_path`, `schemars`, `serde`, `ts_rs`
- **被引用方**: `protocol.rs`、`approvals.rs`、`permissions.rs`、`request_permissions.rs` 以及 `app-server-protocol` 各处

## 实现备注

- 文件超过 1000 行，是 protocol crate 中最核心的数据模型文件。
- 使用 `LazyLock<Template>` 预编译沙箱模式的指令模板。
- `SandboxPermissions` 提供了三种精细化的沙箱覆盖策略，支持逐命令权限升级或扩展。
