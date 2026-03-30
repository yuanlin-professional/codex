# `lib.rs` — 基于 Token 授权的就绪标志

## 功能概述

提供 `ReadinessFlag` 就绪标志实现及 `Readiness` trait，支持基于 token 的授权和异步等待。调用方通过 `subscribe` 获取 token，持有 token 后调用 `mark_ready` 将标志标记为就绪。无订阅者时 `is_ready` 自动返回 `true`。内部使用 `AtomicBool` 进行廉价读取，`watch` 通道广播就绪事件。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ReadinessFlag` | struct | 就绪标志实现 |
| `Token` | struct | 不透明订阅 token |
| `Readiness` | trait | 就绪标志公共接口 |
| `ReadinessError` | enum | 错误：`TokenLockFailed`、`FlagAlreadyReady` |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `is_ready` | `fn(&self) -> bool` | 检查是否就绪 |
| `subscribe` | `async fn(&self) -> Result<Token>` | 订阅并获取 token |
| `mark_ready` | `async fn(&self, token) -> Result<bool>` | 标记为就绪 |
| `wait_ready` | `async fn(&self)` | 异步等待就绪 |

## 依赖关系

- **引用的 crate**: `tokio`, `async_trait`, `thiserror`
- **被引用方**: 系统启动流程中的就绪同步

## 实现备注

- Token 为递增 `i32`，跳过 0 值，重复检测使用 `HashSet`。
- 锁超时为 1 秒，超时返回 `TokenLockFailed`。
- 测试覆盖了订阅-标记-就绪往返、重复标记、无订阅者自动就绪、锁争用等场景。
