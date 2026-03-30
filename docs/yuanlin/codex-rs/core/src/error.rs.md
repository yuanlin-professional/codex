# `error.rs` — 核心错误类型定义与用户友好错误消息格式化

## 功能概述

该文件定义了 Codex 核心库的完整错误类型体系。`CodexErr` 枚举涵盖了所有可能的运行时错误场景，包括网络请求失败、上下文窗口超限、沙箱执行错误、认证失败、用量限制达到等。每个错误变体都实现了用户友好的 `Display` 格式化，并通过 `is_retryable` 方法区分可重试与不可重试错误。此外还提供了错误到协议层 `CodexErrorInfo` 的映射、UI 展示消息的截断处理，以及针对不同付费计划的用量限制友好提示。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `CodexErr` | enum | 核心错误枚举，约 25+ 变体，覆盖所有运行时错误场景 |
| `SandboxErr` | enum | 沙箱相关错误：执行被拒、超时、信号杀死、seccomp/landlock 设置失败 |
| `UnexpectedResponseError` | struct | HTTP 异常响应错误，含状态码、响应体、URL、Cloudflare Ray ID 等 |
| `UsageLimitReachedError` | struct | 用量限制错误，含计划类型、重置时间、速率限制快照、推广消息 |
| `RetryLimitReachedError` | struct | 重试次数超限错误 |
| `ConnectionFailedError` | struct | 连接失败错误 |
| `ResponseStreamFailed` | struct | 响应流读取失败错误 |
| `EnvVarError` | struct | 缺少环境变量错误 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `CodexErr::is_retryable` | `fn(&self) -> bool` | 判断错误是否可以自动重试 |
| `CodexErr::to_codex_protocol_error` | `fn(&self) -> CodexErrorInfo` | 转换为协议层错误分类 |
| `CodexErr::to_error_event` | `fn(&self, message_prefix: Option<String>) -> ErrorEvent` | 转换为可发送的错误事件 |
| `CodexErr::http_status_code_value` | `fn(&self) -> Option<u16>` | 提取关联的 HTTP 状态码 |
| `get_error_message_ui` | `fn(e: &CodexErr) -> String` | 为 UI 展示生成截断后的错误消息（最大 2KiB） |
| `UsageLimitReachedError::fmt` | `fn(&self, f: &mut Formatter)` | 根据不同付费计划生成定制化的用量限制提示 |

## 依赖关系

- **引用的 crate**: `thiserror`（派生错误实现）、`reqwest`（HTTP 状态码类型）、`chrono`（时间格式化）、`codex_login`（认证和计划类型）、`codex_protocol`（错误信息协议类型）、`codex_utils_output_truncation`（文本截断）
- **被引用方**: 几乎所有核心模块都依赖此错误类型；`Result<T>` 类型别名被广泛使用

## 实现备注

- `UsageLimitReachedError` 针对 Free/Go、Plus、Pro、Team/Business、Enterprise 等不同计划生成差异化的升级引导消息。
- `UnexpectedResponseError` 特殊处理了 Cloudflare 403 拦截场景，生成更有帮助的"区域受限"提示。
- `format_retry_timestamp` 智能判断重置时间是否在当天，当天只显示时分，跨天则显示完整日期。
- `day_suffix` 实现了英文序数后缀（1st、2nd、3rd、11th-13th 例外）。
- `CancelErr` 通过 `From` trait 自动转换为 `TurnAborted`。
- 错误消息 UI 截断限制为 2KiB 以避免 TUI 显示问题。
