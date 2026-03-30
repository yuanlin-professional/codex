# readiness

## 功能概述

`codex-utils-readiness` 实现了一个基于 Token 授权的异步就绪标志（readiness flag）。组件可以订阅该标志获取 Token，然后在准备就绪时使用 Token 将标志标记为就绪。其他组件可以异步等待就绪状态。

设计特点：
- 使用原子布尔值实现廉价的就绪状态读取
- 通过 `watch` 通道广播就绪状态给异步等待者
- 若无订阅者，`is_ready()` 调用会自动将标志标记为就绪
- 就绪状态不可逆

## 目录结构

```
readiness/
├── Cargo.toml
└── src/
    └── lib.rs          # ReadinessFlag 实现与 Readiness trait
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `async-trait` | 异步 trait 支持 |
| `thiserror` | 错误类型派生 |
| `time` | 时间处理 |
| `tokio` | 异步互斥锁、watch 通道、超时 |

## 核心接口/API

### `Token`

不透明的订阅令牌，由 `subscribe()` 返回，传给 `mark_ready()` 验证。

### `Readiness` trait

```rust
#[async_trait]
pub trait Readiness: Send + Sync + 'static {
    fn is_ready(&self) -> bool;                                   // 检查是否就绪
    async fn subscribe(&self) -> Result<Token, ReadinessError>;   // 订阅获取令牌
    async fn mark_ready(&self, token: Token) -> Result<bool, ReadinessError>; // 标记就绪
    async fn wait_ready(&self);                                   // 异步等待就绪
}
```

### `ReadinessFlag`

`Readiness` trait 的标准实现。

```rust
let flag = ReadinessFlag::new();

// 订阅
let token = flag.subscribe().await?;

// 标记就绪
flag.mark_ready(token).await?;

// 异步等待（在另一个任务中）
flag.wait_ready().await;
```

### `ReadinessError`

错误枚举：
- `TokenLockFailed` -- 获取锁超时（1 秒）
- `FlagAlreadyReady` -- 标志已就绪，无法再订阅
