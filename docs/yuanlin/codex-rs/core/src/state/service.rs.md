# `service.rs` — 会话级共享服务容器

## 功能概述

`service.rs` 定义了 `SessionServices` 结构体，作为整个会话生命周期中所有共享服务的集中持有者。它将 MCP 连接管理、统一执行管理、分析事件上报、Hook 机制、认证管理、模型管理、插件管理、工具审批、沙箱策略、网络代理等多个子系统的实例聚合在一起，使得会话内的各个模块可以通过单一入口访问所有运行时服务。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `SessionServices` | struct | 会话范围的服务聚合容器，持有所有子系统的共享引用 |

## 关键函数/方法

该文件仅定义了 `SessionServices` 结构体的字段，无公开方法。

## 依赖关系

- **引用的 crate**: `std::sync::Arc`, `tokio::sync::{Mutex, RwLock, watch}`, `tokio_util::sync::CancellationToken`, `codex_analytics`, `codex_exec_server`, `codex_hooks`, `codex_otel`
- **引用的内部模块**: `AuthManager`, `RolloutRecorder`, `SkillsManager`, `AgentControl`, `ModelClient`, `ExecPolicyManager`, `McpManager`, `McpConnectionManager`, `ModelsManager`, `PluginsManager`, `SkillsWatcher`, `StateDbHandle`, `CodeModeService`, `NetworkApprovalService`, `ApprovalStore`, `UnifiedExecProcessManager`, `Shell`, `ShellSnapshot`, `StartedNetworkProxy`
- **被引用方**: `SessionState`、`Session` 等会话核心模块通过持有 `SessionServices` 来访问各子系统

## 实现备注

- 所有字段均为 `pub(crate)` 可见性，限制在 crate 内部使用
- 大量使用 `Arc` 和 `Mutex`/`RwLock` 进行跨异步任务的共享与同步
- `shell_zsh_path` 和 `main_execve_wrapper_exe` 字段通过 `#[cfg_attr(not(unix), allow(dead_code))]` 标注，仅在 Unix 平台有实际用途
- `model_client` 是会话范围的模型客户端，在多个 turn 之间共享复用
