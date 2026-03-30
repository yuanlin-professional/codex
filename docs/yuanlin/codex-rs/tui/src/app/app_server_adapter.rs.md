# `app_server_adapter.rs` — TUI 与 app-server 之间的临时适配层

## 功能概述

本模块是 TUI 和 app-server 之间在混合迁移期间的临时适配层。它处理来自 app-server 的事件流（通知和请求），将 `ServerNotification` 路由到对应的线程，将 `ServerRequest` 分发给主线程或子线程，并提供线程快照到协议事件的转换逻辑。随着更多 TUI 流程迁移到 app-server 表面，此适配器将逐渐缩小并最终消失。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ServerNotificationThreadTarget` | enum | 通知的目标分类：`Thread(ThreadId)`、`InvalidThreadId`、`Global` |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `handle_app_server_event` | `(&mut self, client, event)` | 处理 app-server 事件的主入口，分派通知、请求和断开事件 |
| `handle_server_notification_event` | `(&mut self, client, notification)` | 路由服务器通知到正确的线程或全局处理 |
| `handle_server_request_event` | `(&mut self, client, request)` | 处理服务器请求，包括拒绝不支持的请求类型 |
| `server_notification_thread_target` | `(notification) -> ThreadTarget` | 从通知中提取目标线程 ID |
| `thread_snapshot_events` | `(thread, show_raw) -> Vec<Event>` | 将线程快照转换为协议事件序列（仅测试） |
| `server_notification_thread_events` | `(notification) -> Option<(ThreadId, Vec<Event>)>` | 将服务器通知桥接为核心协议事件（仅测试） |

## 依赖关系

- **引用的 crate**: `codex_app_server_client`, `codex_app_server_protocol`, `codex_protocol`
- **被引用方**: `App::handle_app_server_event` 在主事件循环中调用

## 实现备注

- 大量 `#[cfg(test)]` 代码用于在测试中将 app-server 协议事件桥接为核心协议事件
- 命令执行项需要特殊处理，转换为 `ExecCommandBegin`/`ExecCommandEnd` 事件
- 包含 10+ 个测试用例验证各种通知桥接场景
