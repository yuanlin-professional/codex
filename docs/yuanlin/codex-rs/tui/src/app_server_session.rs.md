# `app_server_session.rs` — App Server 会话管理与 RPC 封装

## 功能概述

本文件定义了 `AppServerSession` 结构体，作为 TUI 与 App Server（嵌入式或远程）之间的会话管理层。它封装了所有 JSON-RPC 请求的发送逻辑，包括账户信息获取、模型列表、线程生命周期管理（创建/恢复/分叉）、轮次管理（启动/中断/引导）、回滚、实时音频会话、技能列表、代码审查等。

此外还提供了一系列辅助函数用于将 App Server 协议类型映射为核心协议类型（如 RateLimitSnapshot、ModelPreset），以及从配置构建线程启动参数的逻辑。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `AppServerSession` | 结构体 | 持有 AppServerClient 和请求 ID 计数器，封装所有 RPC 调用 |
| `AppServerBootstrap` | 结构体 | 启动时获取的初始化数据：账户信息、默认模型、可用模型列表、速率限制 |
| `ThreadSessionState` | 结构体 | 线程会话状态：线程 ID、模型、审批策略、沙箱策略、CWD、历史元数据等 |
| `AppServerStartedThread` | 结构体 | 新启动线程的会话状态和历史轮次 |
| `ThreadParamsMode` | 枚举 | 嵌入式（Embedded）或远程（Remote）模式，影响参数传递行为 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `bootstrap` | `async fn(&mut self, config) -> Result<AppServerBootstrap>` | 获取账户和模型信息用于 TUI 初始化 |
| `start_thread` | `async fn(&mut self, config) -> Result<AppServerStartedThread>` | 创建新线程 |
| `resume_thread` | `async fn(&mut self, config, thread_id) -> Result<AppServerStartedThread>` | 恢复已有线程 |
| `fork_thread` | `async fn(&mut self, config, thread_id) -> Result<AppServerStartedThread>` | 分叉线程 |
| `turn_start` | `async fn(&mut self, thread_id, items, ...) -> Result<TurnStartResponse>` | 启动新的用户轮次 |
| `turn_interrupt` | `async fn(&mut self, thread_id, turn_id) -> Result<()>` | 中断进行中的轮次 |
| `thread_rollback` | `async fn(&mut self, thread_id, num_turns) -> Result<ThreadRollbackResponse>` | 线程回滚 |
| `shutdown` | `async fn(self) -> io::Result<()>` | 关闭会话 |

## 依赖关系

- **引用的 crate**: `codex_app_server_client`、`codex_app_server_protocol`（大量协议类型）、`codex_core::config`、`codex_protocol`
- **被引用方**: `app.rs`（App 持有 AppServerSession 实例并在事件循环中调用其方法）、`lib.rs`

## 实现备注

- 远程模式下，CWD 和 model_provider 等本地信息不传递给 App Server，由后端自行管理。
- 请求 ID 使用简单的自增计数器。
- 历史元数据（log_id 和 entry_count）通过 `message_history::history_metadata` 异步获取。
- 包含完整测试，覆盖嵌入式/远程参数差异、恢复响应的轮次映射、历史元数据填充等场景。
