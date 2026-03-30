# `fs_watch.rs` — 文件系统监控管理器（watch/unwatch）

## 功能概述

本模块实现了 `FsWatchManager`，为每个客户端连接提供文件系统路径监控能力。客户端通过 `fs/watch` 注册监控路径，获取一个 UUID v7 格式的 watch ID；变更事件经过 200ms 去抖后以 `FsChanged` 通知推送到对应连接。`fs/unwatch` 取消监控，连接断开时自动清理该连接的所有监控。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `FsWatchManager` | 结构体 | 文件监控管理器，持有 `FileWatcher` 和连接级监控状态 |
| `DebouncedReceiver` | 结构体 | 内部去抖接收器，合并指定时间窗口内的路径变更事件 |
| `WatchKey` | 结构体 | `ConnectionId` + `watch_id` 的复合键 |
| `WatchEntry` | 结构体 | 单个监控条目，包含终止信号和文件监控订阅 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `watch` | `async fn(&self, connection_id, params) -> Result<FsWatchResponse, ...>` | 注册文件监控并返回 watch ID |
| `unwatch` | `async fn(&self, connection_id, params) -> Result<FsUnwatchResponse, ...>` | 取消监控（仅所有者连接可操作） |
| `connection_closed` | `async fn(&self, connection_id)` | 清理指定连接的所有监控 |

## 依赖关系

- **引用的 crate**: `codex_core::file_watcher`（`FileWatcher`、`FileWatcherSubscriber`）、`codex_app_server_protocol`、`uuid`
- **被引用方**: `message_processor.rs`（处理 `fs/watch` 和 `fs/unwatch` 请求时调用）

## 实现备注

- 监控任务在独立 `tokio::spawn` 中运行，通过 oneshot channel 实现精确终止（保证 unwatch 响应后不再发送通知）。
- 变更路径会相对于监控根路径进行规范化处理。
- 包含测试覆盖 watch ID 格式、连接级作用域隔离、连接断开清理等场景。
