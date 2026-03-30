# `user_notification.rs` -- 用户通知钩子（重复文件）

## 功能概述

此文件与 `legacy_notify.rs` 内容完全相同，定义了旧式用户通知机制。它提供 `legacy_notify_json` 函数将 `AfterAgent` 事件序列化为 `AgentTurnComplete` JSON 载荷，以及 `notify_hook` 函数创建一个以 fire-and-forget 方式执行外部命令的 Hook。通知载荷作为命令行最后一个参数传递，使用 kebab-case 字段命名保持向后兼容。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `UserNotification` | enum | 通知载荷枚举，目前仅包含 `AgentTurnComplete` 变体 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `legacy_notify_json` | `fn(payload: &HookPayload) -> Result<String, serde_json::Error>` | 将 HookPayload 的 AfterAgent 事件序列化为 JSON 字符串 |
| `notify_hook` | `fn(argv: Vec<String>) -> Hook` | 创建一个旧式通知 Hook，fire-and-forget 方式执行外部命令 |

## 依赖关系

- **引用的 crate**: `serde`, `serde_json`, `crate::Hook`, `crate::HookEvent`, `crate::HookPayload`, `crate::HookResult`
- **被引用方**: `registry.rs` 中的 `Hooks::new`

## 实现备注

- 仅支持 `AfterAgent` 事件，其他事件类型返回错误
- 子进程的 stdin/stdout/stderr 全部重定向到 null
- 命令启动失败返回 `FailedContinue`（不阻止后续钩子执行）
- 包含测试验证序列化后的 JSON 结构与历史 wire shape 一致
