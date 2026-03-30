# `manager_tests.rs` -- 测试模块

## 测试覆盖范围

覆盖 `manager.rs` 中的 `ModelsManager` 核心功能，包括模型信息查找、远程刷新、缓存命中/失效、ETag 比较、认证模式过滤、自定义目录、命名空间匹配、优先级排序以及遥测标签发射。使用 wiremock MockServer 模拟远程 /models 端点。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `get_model_info_tracks_fallback_usage` | 验证已知模型不使用回退元数据，未知模型标记为 `used_fallback_model_metadata` |
| `get_model_info_uses_custom_catalog` | 验证自定义目录中的模型属性（如 supports_image_detail_original）被正确应用 |
| `get_model_info_matches_namespaced_suffix` | 验证 `custom/gpt-image` 格式正确匹配 `gpt-image` slug |
| `get_model_info_rejects_multi_segment_namespace_suffix_matching` | 验证多层命名空间（ns1/ns2/model）不进行后缀匹配 |
| `refresh_available_models_sorts_by_priority` | 验证刷新后模型按优先级排序 |
| `refresh_available_models_uses_cache_when_fresh` | 验证第二次刷新使用缓存，不发起额外网络请求 |
| `refresh_available_models_refetches_when_cache_stale` | 验证缓存过期后重新从远程获取 |
| `refresh_available_models_refetches_when_version_mismatch` | 验证客户端版本不匹配时重新从远程获取 |
| `refresh_available_models_drops_removed_remote_models` | 验证远程移除的模型不再出现在可用列表中 |
| `refresh_available_models_skips_network_without_chatgpt_auth` | 验证非 ChatGPT 认证下不发起网络请求 |
| `models_request_telemetry_emits_auth_env_feedback_tags_on_failure` | 验证 /models 请求失败时正确发射认证环境反馈标签 |
| `build_available_models_picks_default_after_hiding_hidden_models` | 验证隐藏模型不影响默认模型选择 |
| `bundled_models_json_roundtrips` | 验证内置 models.json 可以正确序列化/反序列化往返 |
