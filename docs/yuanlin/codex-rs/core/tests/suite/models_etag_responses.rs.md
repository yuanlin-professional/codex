# `models_etag_responses.rs` — 测试模块

## 测试覆盖范围
模型 ETag 响应处理，验证 X-Models-Etag 不匹配时触发 /models 刷新，以及避免重复刷新的去重逻辑。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `refresh_models_on_models_etag_mismatch_and_avoid_duplicate_models_fetch` | 当 /responses 响应中的 X-Models-Etag 与缓存不匹配时刷新 /models，后续相同 ETag 不再重复刷新 |
