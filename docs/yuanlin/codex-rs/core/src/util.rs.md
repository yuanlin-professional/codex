# `util.rs` — 核心通用工具函数和反馈标签系统

## 功能概述

提供 Codex Core 中的通用工具函数和结构化反馈遥测（feedback telemetry）基础设施。包含 `feedback_tags!` 宏用于向 tracing 系统发送结构化反馈元数据、认证相关的反馈标签发送函数、指数退避重试计算、路径解析、线程名称规范化以及恢复命令生成等实用功能。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `FeedbackRequestTags` | 结构体 | 封装 HTTP 请求反馈标签字段，包含认证头、模式、恢复状态等遥测信息 |
| `FeedbackRequestSnapshot` | 结构体（内部） | 将 `FeedbackRequestTags` 中的 `Option` 字段规范化为空字符串以便发送 |
| `Auth401FeedbackSnapshot` | 结构体（内部） | 封装 401 认证错误场景的反馈快照字段 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `feedback_tags!` | 宏 `($key = $value),+` | 向 tracing 系统发送结构化反馈标签（target: "feedback_tags"） |
| `emit_feedback_request_tags` | `(tags: &FeedbackRequestTags)` | 发送 HTTP 请求相关的反馈遥测标签（仅测试时可用） |
| `emit_feedback_request_tags_with_auth_env` | `(tags, auth_env)` | 发送带认证环境信息的请求反馈标签 |
| `emit_feedback_auth_recovery_tags` | `(mode, phase, outcome, ...)` | 发送认证恢复流程的反馈标签 |
| `backoff` | `(attempt: u64) -> Duration` | 计算带抖动的指数退避延迟（200ms 基础 * 2^n） |
| `error_or_panic` | `(message)` | debug 模式下 panic，release 模式下记录错误日志 |
| `resolve_path` | `(base, path) -> PathBuf` | 将相对路径相对于 base 解析为绝对路径 |
| `normalize_thread_name` | `(name: &str) -> Option<String>` | 去除首尾空白，空字符串返回 None |
| `resume_command` | `(thread_name, thread_id) -> Option<String>` | 生成 `codex resume` CLI 命令字符串 |

## 依赖关系

- **引用的 crate**: `rand`、`tracing`、`codex_protocol::ThreadId`
- **被引用方**: `codex-core` 内部多处（HTTP 客户端、认证恢复、session 管理等）

## 实现备注

- `feedback_tags!` 宏通过 tracing `info!` 事件的 `target: "feedback_tags"` 与 `codex_feedback::CodexFeedback::metadata_layer()` 配合使用，实现反馈元数据的自动采集。
- `backoff` 函数的抖动范围为 0.9~1.1 倍，避免多客户端同时重试导致的"惊群效应"。
- 测试模块位于外部文件 `util_tests.rs`。
