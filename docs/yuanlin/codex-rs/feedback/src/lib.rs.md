# `lib.rs` — 反馈收集与上传系统

## 功能概述

提供 Codex 反馈收集和上传功能。核心组件是 `CodexFeedback`，它维护一个固定容量（默认 4MiB）的环形缓冲区捕获完整 TRACE 级别日志，以及一个结构化标签收集层用于附加元数据。支持将反馈快照（日志 + 标签 + 连接性诊断）通过 Sentry DSN 上传，或保存到临时文件。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `CodexFeedback` | struct | 反馈收集器，包装 Arc<FeedbackInner> |
| `FeedbackSnapshot` | struct | 反馈快照：日志字节、标签、诊断信息、线程 ID |
| `RingBuffer` | struct (内部) | 固定容量环形缓冲区，超出时丢弃前部 |
| `FeedbackMetadataLayer` | struct (内部) | tracing Layer，收集 feedback_tags target 的键值标签 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `logger_layer` | `(&self) -> impl Layer<S>` | 创建 TRACE 级别日志捕获层 |
| `metadata_layer` | `(&self) -> impl Layer<S>` | 创建 feedback_tags 标签收集层 |
| `snapshot` | `(&self, session_id) -> FeedbackSnapshot` | 创建当前反馈快照 |
| `upload_feedback` | `(&self, classification, reason, include_logs, ...)` | 通过 Sentry 上传反馈 |

## 依赖关系

- **引用的 crate**: `codex_protocol`、`sentry`、`tracing`、`tracing_subscriber`
- **被引用方**: TUI 反馈功能、exec/CLI 启动
