# `lib.rs` — 基于 Tokio 互斥锁的 LRU 缓存

## 功能概述

本文件提供 `BlockingLruCache<K, V>`，一个受 Tokio `Mutex` 保护的最小化 LRU 缓存实现。当没有可用的 Tokio 运行时时，所有缓存操作退化为无操作（no-op），确保在非异步上下文中安全使用。此外还提供 `sha1_digest` 辅助函数，用于基于内容的缓存键计算。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `BlockingLruCache<K, V>` | struct | 线程安全的 LRU 缓存，容量非零 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn(capacity: NonZeroUsize) -> Self` | 创建指定容量的缓存 |
| `get_or_insert_with` | `fn(&self, key, value_fn) -> V` | 获取缓存值或计算并插入 |
| `get_or_try_insert_with` | `fn(&self, key, value_fn) -> Result<V, E>` | 可失败版本的 get-or-insert |
| `get` | `fn(&self, key) -> Option<V>` | 获取缓存值的克隆 |
| `insert` | `fn(&self, key, value) -> Option<V>` | 插入值，返回旧值 |
| `remove` | `fn(&self, key) -> Option<V>` | 移除并返回值 |
| `clear` | `fn(&self)` | 清空缓存 |
| `sha1_digest` | `fn(bytes: &[u8]) -> [u8; 20]` | 计算 SHA-1 摘要 |

## 依赖关系

- **引用的 crate**: `lru`, `sha1`, `tokio`
- **被引用方**: `codex-utils-image` 等需要缓存功能的 crate

## 实现备注

- 使用 `tokio::runtime::Handle::try_current()` 检测运行时可用性，不可用时缓存操作为 no-op。
- `with_mut` 方法在无运行时时创建 unbounded 临时缓存供回调使用。
