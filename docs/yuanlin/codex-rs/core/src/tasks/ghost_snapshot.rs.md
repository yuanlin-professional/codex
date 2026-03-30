# `ghost_snapshot.rs` — Ghost 快照任务

## 功能概述

实现了 `GhostSnapshotTask`，负责在每轮对话开始时为当前 Git 仓库创建一个 "ghost commit"（幽灵提交）快照。该快照用于支持撤销操作，允许用户恢复到 AI 修改之前的文件状态。任务在专用的阻塞线程池中运行 Git 操作，支持超时警告（240 秒后提醒用户检查大文件）、取消机制，以及对大型未跟踪目录和文件的警告格式化。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `GhostSnapshotTask` | 结构体 | Ghost 快照任务，持有 `Token` 用于标记就绪门控 |
| `SNAPSHOT_WARNING_THRESHOLD` | 常量 | 快照操作超时警告阈值（240 秒） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn(token: Token) -> Self` | 创建新的快照任务 |
| `run` | `async fn(self: Arc<Self>, session, ctx, _input, cancellation_token) -> Option<String>` | 执行快照：启动超时警告任务、在阻塞线程中创建 ghost commit、格式化并发送警告、记录快照到对话历史 |
| `format_snapshot_warnings` | `fn(ignore_large_untracked_files, ignore_large_untracked_dirs, report) -> Vec<String>` | 汇总格式化大文件/大目录警告消息 |
| `format_large_untracked_warning` | `fn(ignore_large_untracked_dirs, report) -> Option<String>` | 格式化大型未跟踪目录的警告消息 |
| `format_ignored_untracked_files_warning` | `fn(ignore_large_untracked_files, report) -> Option<String>` | 格式化被忽略的大型未跟踪文件的警告消息 |
| `format_bytes` | `fn(bytes: i64) -> String` | 将字节数格式化为可读的大小字符串（B/KiB/MiB） |

## 依赖关系

- **引用的 crate**: `async_trait`、`tokio`、`tracing`、`codex_git_utils`（ghost commit 操作）、`codex_utils_readiness`（就绪门控）
- **引用的内部模块**: `crate::codex::TurnContext`、`crate::protocol`（EventMsg、WarningEvent）、`crate::state::TaskKind`
- **被引用方**: `tasks/mod.rs`（re-export 为 `GhostSnapshotTask`）

## 实现备注

- Git 操作必须在 `spawn_blocking` 中执行，因为底层 libgit2 操作不是异步的。
- 使用 `oneshot` 通道在快照完成时取消超时警告任务，避免误报。
- 快照结果通过 `tool_call_gate.mark_ready(token)` 标记就绪，解除工具调用的等待门控。
- 警告消息最多显示 3 个大目录/文件条目，超出部分显示 "N more"。
- 若 `disable_warnings` 配置为 true，则完全跳过所有警告逻辑。
