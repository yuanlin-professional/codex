# `types.rs` -- Hooks 系统核心类型定义

## 功能概述

该文件定义了 Codex hooks 系统的核心类型，包括 Hook 函数类型别名、Hook 执行结果枚举、Hook 载荷结构体、以及各种事件类型的数据结构。Hook 载荷采用 tagged union 模式（`HookEvent` 枚举），支持 `AfterAgent`（代理完成后）和 `AfterToolUse`（工具使用后）两类事件。所有类型都支持 serde 序列化以便通过 IPC 传递。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `HookFn` | type alias | Hook 函数类型：`Arc<dyn Fn(&HookPayload) -> BoxFuture<HookResult>>` |
| `HookResult` | enum | 执行结果：`Success`、`FailedContinue`（失败但继续）、`FailedAbort`（失败并中止） |
| `HookResponse` | struct | Hook 执行响应，包含钩子名称和结果 |
| `Hook` | struct | 钩子实例，包含名称和函数指针 |
| `HookPayload` | struct | 钩子事件载荷，包含 session_id、cwd、client、triggered_at、hook_event |
| `HookEvent` | enum | 事件类型枚举：`AfterAgent` 和 `AfterToolUse` |
| `HookEventAfterAgent` | struct | 代理完成事件数据：thread_id、turn_id、input_messages、last_assistant_message |
| `HookEventAfterToolUse` | struct | 工具使用后事件数据：包含工具名、输入、执行结果、耗时、沙箱策略等详细信息 |
| `HookToolKind` | enum | 工具类型：Function、Custom、LocalShell、Mcp |
| `HookToolInput` | enum | 工具输入：Function、Custom、LocalShell、Mcp 四种变体 |
| `HookToolInputLocalShell` | struct | 本地 shell 工具输入参数 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `HookResult::should_abort_operation` | `fn(&self) -> bool` | 判断是否应中止操作 |
| `Hook::execute` | `async fn(&self, payload: &HookPayload) -> HookResponse` | 执行钩子并返回响应 |

## 依赖关系

- **引用的 crate**: `chrono`, `codex_protocol`, `futures`, `serde`
- **被引用方**: `registry.rs`, `legacy_notify.rs`, 以及所有 hooks 事件处理模块

## 实现备注

- `Hook` 实现了 `Default` trait，默认返回 `HookResult::Success`
- `HookPayload` 的 `triggered_at` 使用自定义序列化函数输出 RFC 3339 秒精度格式
- `HookEvent` 使用 `tag = "event_type"` 的 serde 标签模式
- 包含测试验证 `AfterAgent` 和 `AfterToolUse` 事件的 JSON 序列化 wire shape 稳定性
