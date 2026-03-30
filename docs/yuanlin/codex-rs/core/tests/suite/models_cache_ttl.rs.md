# `models_cache_ttl.rs` — 测试模块

## 测试覆盖范围
模型缓存 TTL（生存时间）与版本管理，验证 ETag 匹配时的缓存续期、客户端版本匹配时的缓存复用、版本缺失或不匹配时的刷新行为。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `renews_cache_ttl_on_matching_models_etag` | 响应中的 X-Models-Etag 匹配时续期缓存 TTL，无需重新请求 /models |
| `uses_cache_when_version_matches` | 缓存版本与客户端版本匹配时直接使用缓存，不请求 /models |
| `refreshes_when_cache_version_missing` | 缓存中无版本信息时触发 /models 刷新 |
| `refreshes_when_cache_version_differs` | 缓存版本与客户端版本不匹配时触发 /models 刷新 |
