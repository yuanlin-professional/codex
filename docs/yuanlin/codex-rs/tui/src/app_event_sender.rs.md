# `app_event_sender.rs` — 应用事件发送器

## 功能概述

本文件定义了 `AppEventSender` 结构体，封装了一个 tokio 无界通道发送端，提供向 `App` 事件循环发送 `AppEvent` 的便捷方法。它作为 widget 层与 app 层之间的桥梁，使得 widget 无需直接持有 `App` 的引用即可触发操作。发送失败时会通过 tracing 记录错误而非 panic，增强了容错性。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `AppEventSender` | 结构体 | 封装 `UnboundedSender<AppEvent>`，可 Clone，提供多种便捷发送方法 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `send` | `fn(&self, event: AppEvent)` | 发送事件到 app 通道，失败时记录日志；非 CodexOp 事件还会记录会话日志 |
| `interrupt` | `fn(&self)` | 发送中断命令 |
| `compact` | `fn(&self)` | 发送上下文压缩命令 |
| `exec_approval` | `fn(&self, thread_id, id, decision)` | 发送执行审批决策到指定线程 |
| `patch_approval` | `fn(&self, thread_id, id, decision)` | 发送补丁审批决策到指定线程 |
| `resolve_elicitation` | `fn(&self, thread_id, server_name, request_id, decision, content, meta)` | 解决 MCP 服务器引导请求 |

## 依赖关系

- **引用的 crate**: `tokio::sync::mpsc`、`codex_protocol`、`crate::app_command`、`crate::app_event`、`crate::session_log`
- **被引用方**: `chatwidget.rs`、`bottom_pane` 等需要向 app 层发送事件的组件

## 实现备注

所有发送方法在内部将 `AppCommand` 转换为 `Op` 后包装为 `AppEvent::CodexOp` 或 `AppEvent::SubmitThreadOp`。入站事件（非 CodexOp）会通过 `session_log` 记录，用于高保真会话回放。
