# `lib.rs` — 异步取消扩展 trait

## 功能概述

提供 `OrCancelExt` trait，为任意 `Future` 添加 `.or_cancel(token)` 方法。当 CancellationToken 被触发时，future 会提前返回 `Err(CancelErr::Cancelled)`；否则返回 `Ok(result)`。基于 `tokio::select!` 实现。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `CancelErr` | enum | 取消错误类型，仅有 `Cancelled` 变体 |
| `OrCancelExt` | trait | 为 Future 提供 `or_cancel` 方法 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `or_cancel` | `async (self, token) -> Result<Output, CancelErr>` | 将 future 与取消 token 竞争执行 |

## 依赖关系

- **引用的 crate**: `async_trait`、`tokio_util`
- **被引用方**: 需要可取消异步操作的模块
