# `legacy_notify.rs` -- 旧式通知钩子实现

## 功能概述

该文件实现了向后兼容的旧式用户通知机制。它将 `AfterAgent` 事件序列化为 `AgentTurnComplete` JSON 载荷，并作为命令行最后一个参数传递给外部通知命令。通知以 fire-and-forget 方式发送，子进程的 stdin/stdout/stderr 全部重定向到 null。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `UserNotification` | enum | 通知载荷枚举，包含 `AgentTurnComplete` 变体，字段使用 kebab-case 命名 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `legacy_notify_json` | `fn(payload: &HookPayload) -> Result<String, serde_json::Error>` | 将 AfterAgent 事件序列化为旧式通知 JSON |
| `notify_hook` | `fn(argv: Vec<String>) -> Hook` | 创建旧式通知 Hook，异步启动外部命令并附加 JSON 参数 |

## 依赖关系

- **引用的 crate**: `serde`, `serde_json`, `std::process::Stdio`, `std::sync::Arc`
- **被引用方**: `registry.rs` 在初始化时使用 `notify_hook` 创建旧式通知钩子

## 实现备注

- 仅支持 `AfterAgent` 事件，其他事件类型返回 IO 错误
- 命令执行失败返回 `HookResult::FailedContinue`，不影响后续钩子执行
- 包含 2 个测试：验证 `UserNotification` 序列化格式和 `legacy_notify_json` 的历史 wire shape 兼容性
