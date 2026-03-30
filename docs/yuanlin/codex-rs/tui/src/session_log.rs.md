# `session_log.rs` — 会话日志记录器

## 功能概述

提供可选的会话事件 JSONL 日志记录功能。通过环境变量 `CODEX_TUI_RECORD_SESSION=1`
启用后，将所有入站 AppEvent、出站 AppCommand 和元数据事件记录到 JSONL 文件中。
日志文件路径可通过 `CODEX_TUI_SESSION_LOG_PATH` 自定义，默认写入日志目录。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `SessionLogger` | struct | 全局会话日志记录器（通过 `LazyLock` 初始化） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `maybe_init` | `(config)` | 根据环境变量决定是否初始化日志 |
| `log_inbound_app_event` | `(event)` | 记录入站事件 |
| `log_outbound_op` | `(op)` | 记录出站操作 |
| `log_session_end` | `()` | 记录会话结束 |

## 依赖关系

- **引用的 crate**: `serde`, `serde_json`, `chrono`, `codex_core`
- **引用的模块**: `crate::app_command`, `crate::app_event`

## 实现备注

- 文件权限在 Unix 上设为 0o600（仅所有者读写）。
- 使用 RFC3339 毫秒精度时间戳。
- 对非关键事件仅记录变体名称以减少噪音。
