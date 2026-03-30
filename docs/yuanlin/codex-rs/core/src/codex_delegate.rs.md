# `codex_delegate.rs` — 子代理（Sub-Agent）委托执行与审批转发

## 功能概述

该文件实现了 Codex 子代理线程的创建和管理逻辑。它提供了两种启动子代理的方式：交互式模式（`run_codex_thread_interactive`）和一次性模式（`run_codex_thread_one_shot`）。核心职责是在子代理和父会话之间转发事件流，拦截子代理产生的审批请求（命令执行、补丁应用、权限请求、用户输入等），将其路由到父会话或 Guardian 审核系统进行决策，然后将决策结果回传给子代理。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `Codex` | struct (外部) | 子代理实例，通过 `tx_sub`/`rx_event` 通道与调用方通信 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `run_codex_thread_interactive` | `async fn(...) -> Result<Codex, CodexErr>` | 启动交互式子代理线程，返回事件和操作通道 |
| `run_codex_thread_one_shot` | `async fn(..., input: Vec<UserInput>, ...) -> Result<Codex, CodexErr>` | 启动一次性子代理，自动提交初始输入并在完成后关闭 |
| `forward_events` | `async fn(...)` | 事件转发循环：过滤/分发子代理事件，拦截审批请求路由到父会话 |
| `forward_ops` | `async fn(...)` | 将调用方的操作（Op）转发给子代理 |
| `handle_exec_approval` | `async fn(...)` | 处理命令执行审批：支持 Guardian 审核和普通父会话审批两条路径 |
| `handle_patch_approval` | `async fn(...)` | 处理补丁应用审批：构建 Guardian 审核请求或走普通审批流程 |
| `handle_request_user_input` | `async fn(...)` | 处理用户输入请求：拦截 MCP 工具审批并通过 Guardian 自动决策 |
| `maybe_auto_review_mcp_request_user_input` | `async fn(...)` | 拦截委托的 MCP 工具审批提示，通过 Guardian 自动审核 |
| `spawn_guardian_review` | `fn(...) -> oneshot::Receiver<ReviewDecision>` | 在独立线程中创建 Tokio 运行时执行 Guardian 审核 |
| `await_approval_with_cancel` | `async fn(...)` | 等待审批决策，支持取消令牌中断 |
| `shutdown_delegate` | `async fn(codex: &Codex)` | 优雅关闭子代理：发送中断和关闭信号，排空事件队列 |

## 依赖关系

- **引用的 crate**: `async_channel`（有界通道）、`codex_async_utils`（取消扩展）、`codex_exec_server`（环境管理器）、`codex_protocol`（协议事件、操作、审批类型）、`tokio_util`（CancellationToken）
- **被引用方**: 被工具调用层使用，当模型需要启动子代理执行复杂任务时调用

## 实现备注

- Guardian 审核在独立的 `std::thread` 中运行新建的 `current_thread` Tokio 运行时，避免阻塞主事件循环。
- `pending_mcp_invocations` 使用 `Arc<Mutex<HashMap>>` 缓存 MCP 工具调用上下文，以便后续 `RequestUserInput` 兼容路径能重建完整的审核请求。
- 一次性模式会在收到 `TurnComplete` 或 `TurnAborted` 后自动发送 `Shutdown` 操作并取消子令牌，返回的 `tx_sub` 是已关闭的通道以阻止后续操作。
- 所有审批等待都通过 `tokio::select! { biased; }` 实现，优先响应取消令牌以保证快速清理。
- 事件过滤器忽略了 delta 事件（`AgentMessageDelta`、`AgentReasoningDelta`）、`TokenCount` 和 `SessionConfigured` 等不需要转发的事件。
