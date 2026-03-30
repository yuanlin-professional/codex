# cache

## 功能概述

`codex-utils-cache` 提供了一个受 Tokio 互斥锁保护的线程安全 LRU 缓存实现 `BlockingLruCache`。该缓存在有 Tokio 运行时的环境下正常工作，在没有运行时的情况下优雅降级为无操作（no-op），确保在测试或非异步上下文中不会 panic。

此外还提供了一个 `sha1_digest` 工具函数，用于基于内容的缓存键生成。

## 目录结构

```
cache/
├── Cargo.toml
└── src/
    └── lib.rs          # BlockingLruCache 实现与 sha1_digest 函数
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `lru` | 底层 LRU 缓存数据结构 |
| `sha1` | SHA-1 哈希计算 |
| `tokio` | 异步互斥锁（Mutex）和运行时检测 |

## 核心接口/API

### `BlockingLruCache<K, V>`

```rust
// 创建指定容量的缓存
let cache = BlockingLruCache::new(NonZeroUsize::new(100).unwrap());

// 尝试创建（容量为 0 时返回 None）
let cache = BlockingLruCache::try_with_capacity(100);

// 获取或插入
let value = cache.get_or_insert_with(key, || compute_value());

// 可失败的获取或插入
let value = cache.get_or_try_insert_with(key, || Ok(compute_value()))?;

// 基本操作
cache.insert(key, value);
let v = cache.get(&key);
cache.remove(&key);
cache.clear();

// 直接操作底层缓存
cache.with_mut(|inner| { /* ... */ });
let guard = cache.blocking_lock();
```

### `sha1_digest(bytes: &[u8]) -> [u8; 20]`

计算字节切片的 SHA-1 摘要，适用于生成基于内容的缓存键，避免仅使用路径导致的缓存失效问题。
