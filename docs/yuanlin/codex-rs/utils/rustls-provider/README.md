# rustls-provider

## 功能概述

`codex-utils-rustls-provider` 提供了一个简单的初始化函数，确保在进程级别安装 rustls 加密提供者。当依赖图中同时启用了 `ring` 和 `aws-lc-rs` 特性时，rustls 无法自动选择提供者，此 crate 通过 `Once` 保证全局只初始化一次 `ring` 作为默认提供者。

## 目录结构

```
rustls-provider/
├── Cargo.toml
└── src/
    └── lib.rs          # ensure_rustls_crypto_provider() 函数
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `rustls` | TLS 库，需要安装加密提供者 |

## 核心接口/API

### `ensure_rustls_crypto_provider()`

确保进程范围内已安装 rustls 加密提供者。使用 `Once` 保证幂等性，多次调用安全无副作用。

```rust
// 在程序启动时调用
ensure_rustls_crypto_provider();
// 后续所有 rustls 操作将使用 ring 作为加密后端
```
