# `log_db.rs` — tracing 日志到 SQLite 数据库的导出层

## 功能概述
该模块提供 `tracing_subscriber::Layer` 实现 `LogDbLayer`，将 tracing 事件捕获并批量插入到 SQLite 日志数据库。使用后台 tokio 任务和通道实现异步写入，支持批量插入（128 条/批）和定时刷新（2 秒间隔）。同时管理日志保留策略（10 天过期清理）。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `LogDbLayer` | 结构体 | tracing Layer 实现，通过 mpsc 通道将日志发送到后台写入器 |
| `SpanLogContext` | 结构体(内部) | Span 扩展数据，记录 span 名称、格式化字段和 thread_id |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `start` | `(state_db: Arc<StateRuntime>) -> LogDbLayer` | 启动日志写入器和保留清理任务 |
| `LogDbLayer::flush` | `async (&self)` | 手动刷新缓冲区到数据库 |
| `on_event` | `(&self, event, ctx)` | Layer 回调：捕获事件并发送到写入通道 |
| `run_inserter` | `async (state_db, receiver)` | 后台写入器：批量插入或定时刷新 |
| `run_retention_cleanup` | `async (state_db)` | 启动时清理过期日志 |

## 依赖关系
- **引用的 crate**: `tracing`, `tracing_subscriber`, `tokio`, `uuid`
- **被引用方**: `codex-core` tracing 初始化

## 实现备注
- 日志队列容量 512，溢出时使用 `try_send` 静默丢弃
- thread_id 从 span 扩展数据中继承，支持跨 span 传播
- 每个进程生成唯一 UUID 用于关联无线程日志
