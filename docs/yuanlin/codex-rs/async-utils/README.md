# async-utils

## 功能概述

`codex-async-utils` 是 Codex 项目的异步工具模块，提供基于 `tokio` 的异步编程辅助工具。当前的核心功能是 `OrCancelExt` trait，它为任意 `Future` 添加了与 `CancellationToken` 竞争的能力，使得异步任务可以在取消令牌触发时立即中断并返回取消错误。

## 架构说明

该模块的设计理念是提供简洁的异步扩展 trait，通过组合模式增强标准 Future 的能力：

### OrCancelExt trait

```
Future + CancellationToken -> tokio::select! -> Ok(result) | Err(CancelErr::Cancelled)
```

内部使用 `tokio::select!` 宏同时等待原始 Future 和取消令牌，哪个先完成就返回哪个的结果。这避免了在每个异步函数中手动编写 `select!` 竞争逻辑。

### 使用示例

```rust
use codex_async_utils::OrCancelExt;

let token = CancellationToken::new();
let result = some_async_operation().or_cancel(&token).await;
match result {
    Ok(value) => { /* 正常完成 */ },
    Err(CancelErr::Cancelled) => { /* 被取消 */ },
}
```

## 目录结构

```
async-utils/
  Cargo.toml
  src/
    lib.rs   # 完整实现：OrCancelExt trait 及其 blanket 实现
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `async-trait` | 异步 trait 支持 |
| `tokio` | 异步运行时（`select!` 宏） |
| `tokio-util` | `CancellationToken` 类型 |

## 核心接口/API

### OrCancelExt trait

```rust
#[async_trait]
pub trait OrCancelExt: Sized {
    type Output;

    /// 将 Future 与 CancellationToken 竞争执行
    /// - 若 Future 先完成，返回 Ok(output)
    /// - 若 token 先取消，返回 Err(CancelErr::Cancelled)
    async fn or_cancel(self, token: &CancellationToken) -> Result<Self::Output, CancelErr>;
}
```

该 trait 为所有满足 `Future + Send`（输出也满足 `Send`）的类型提供了 blanket 实现，无需手动为每种 Future 实现。

### 错误类型

```rust
#[derive(Debug, PartialEq, Eq)]
pub enum CancelErr {
    Cancelled,
}
```
