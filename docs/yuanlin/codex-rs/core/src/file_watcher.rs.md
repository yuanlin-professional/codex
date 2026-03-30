# `file_watcher.rs` — 多订阅者文件变更监控系统

## 功能概述

该文件实现了一个基于 `notify` crate 的多订阅者文件监控系统。`FileWatcher` 支持多个独立的订阅者同时注册各自关心的文件/目录路径，当文件系统发生创建、修改或删除事件时，系统自动将变更通知路由到匹配的订阅者。内部通过引用计数机制管理底层 `notify::Watcher` 的路径注册与注销，确保相同路径只被监控一次，并在最后一个订阅者取消注册时自动停止监控。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `FileWatcher` | struct | 多订阅者文件监控器，封装 `notify::RecommendedWatcher` |
| `FileWatcherSubscriber` | struct | 订阅者句柄，RAII 语义，Drop 时自动注销 |
| `WatchRegistration` | struct | 路径注册的 RAII 守卫，Drop 时自动取消路径注册 |
| `Receiver` | struct | 订阅者的事件接收端，提供异步 `recv` 方法 |
| `ThrottledWatchReceiver` | struct | 带节流功能的接收器包装，限制事件发送最小间隔 |
| `FileWatcherEvent` | struct | 合并后的文件变更通知，包含去重排序的变更路径列表 |
| `WatchPath` | struct | 路径订阅配置：根路径 + 是否递归 |
| `PathWatchCounts` | struct | 路径的递归/非递归引用计数 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `FileWatcher::new` | `fn() -> notify::Result<Self>` | 创建活跃的文件监控器并启动后台事件循环 |
| `FileWatcher::noop` | `fn() -> Self` | 创建惰性监控器（仅供测试） |
| `FileWatcher::add_subscriber` | `fn(self: &Arc<Self>) -> (FileWatcherSubscriber, Receiver)` | 添加订阅者，返回注册句柄和事件接收器 |
| `FileWatcherSubscriber::register_paths` | `fn(&self, paths: Vec<WatchPath>) -> WatchRegistration` | 为订阅者注册监控路径，返回 RAII 守卫 |
| `ThrottledWatchReceiver::recv` | `async fn(&mut self) -> Option<FileWatcherEvent>` | 节流接收：确保两次事件间隔不小于配置的最小间隔 |
| `Receiver::recv` | `async fn(&mut self) -> Option<FileWatcherEvent>` | 等待并返回下一批变更路径 |
| `notify_subscribers` | `async fn(state, event_paths)` | 将文件事件路由到匹配的订阅者 |
| `watch_path_matches_event` | `fn(watched_path, event_path) -> bool` | 判断事件路径是否匹配监控路径（考虑递归/非递归） |

## 依赖关系

- **引用的 crate**: `notify`（跨平台文件系统监控）、`tokio`（异步运行时、通道、定时器）、`tracing`
- **被引用方**: MCP 服务器、技能监控器等需要监控配置文件变更的组件

## 实现备注

- 使用自定义的 `WatchSender`/`Receiver` 通道对代替 `tokio::mpsc`，内部通过 `AsyncMutex<BTreeSet<PathBuf>>` + `Notify` 实现变更路径的自动合并去重。
- `WatchSender` 实现了引用计数的 Clone/Drop，当所有发送端被丢弃时自动唤醒等待中的接收端并返回 `None`。
- `PathWatchCounts` 分别追踪递归和非递归的引用计数，当递归计数 > 0 时使用 `RecursiveMode::Recursive`，否则使用 `NonRecursive`。
- `reconfigure_watch` 采用惰性锁获取模式，仅在确实需要重新配置底层 watcher 时才获取锁。
- `is_mutating_event` 过滤器只传递 Create/Modify/Remove 事件，忽略 Access 等只读事件。
- `dedupe_watched_paths` 在注册前对路径列表排序去重，避免重复注册。
