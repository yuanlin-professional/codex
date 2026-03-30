# `cache.rs` -- 模型元数据磁盘缓存管理

## 功能概述

管理模型列表的磁盘缓存，支持加载、保存、TTL 过期检查和续期。缓存以 JSON 文件形式持久化，包含模型元数据列表、ETag、客户端版本和获取时间戳。加载时会校验客户端版本一致性和缓存新鲜度（TTL），不一致或过期的缓存会被跳过。提供测试辅助方法用于操纵缓存内容和时间戳。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ModelsCacheManager` | 结构体 | 缓存管理器，封装缓存文件路径和 TTL 配置 |
| `ModelsCache` | 结构体 | 缓存数据，包含 `fetched_at`、`etag`、`client_version`、`models` 列表 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ModelsCacheManager::new` | `fn(PathBuf, Duration) -> Self` | 创建缓存管理器 |
| `ModelsCacheManager::load_fresh` | `async fn(&self, &str) -> Option<ModelsCache>` | 加载新鲜缓存，校验版本和 TTL |
| `ModelsCacheManager::persist_cache` | `async fn(&self, &[ModelInfo], Option<String>, String)` | 持久化缓存到磁盘 |
| `ModelsCacheManager::renew_cache_ttl` | `async fn(&self) -> io::Result<()>` | 更新 fetched_at 为当前时间以续期 TTL |
| `ModelsCache::is_fresh` | `fn(&self, Duration) -> bool` | 检查缓存是否未超过 TTL |

## 依赖关系

- **引用的 crate**: `chrono`、`codex_protocol::openai_models::ModelInfo`、`serde`、`tokio::fs`
- **被引用方**: `manager.rs` 中的 `ModelsManager` 使用此缓存管理器

## 实现备注

- TTL 为零时 `is_fresh` 始终返回 false，相当于禁用缓存。
- `load_fresh` 在版本不匹配时记录日志并返回 None，触发远程重新获取。
- 保存时自动创建父目录，使用 `serde_json::to_vec_pretty` 生成可读 JSON。
- 提供 `#[cfg(test)]` 专用方法 `set_ttl`、`manipulate_cache_for_test`、`mutate_cache_for_test` 方便测试控制。
