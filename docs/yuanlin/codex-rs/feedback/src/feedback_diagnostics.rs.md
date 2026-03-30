# `feedback_diagnostics.rs` — 反馈连接性诊断

## 功能概述

收集影响网络连接性的环境变量信息作为反馈诊断数据。检测代理环境变量（HTTP_PROXY、HTTPS_PROXY 等）和 OPENAI_BASE_URL 设置，生成结构化诊断报告并格式化为文本附件。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `FeedbackDiagnostics` | struct | 诊断信息集合 |
| `FeedbackDiagnostic` | struct | 单条诊断：标题 + 详情列表 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `collect_from_env` | `() -> Self` | 从当前进程环境变量收集诊断 |
| `attachment_text` | `(&self) -> Option<String>` | 格式化为文本附件内容 |

## 依赖关系

- **被引用方**: `feedback/src/lib.rs`（作为反馈附件）
