# `model_provider_info_tests.rs` — 测试模块

## 测试覆盖范围

测试 `ModelProviderInfo` 结构体从 TOML 格式的反序列化逻辑，覆盖多种模型提供商配置场景（Ollama、Azure、自定义 HTTP 头、WebSocket 超时等），以及已移除的 `chat` wire API 的友好错误提示。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `test_deserialize_ollama_model_provider_toml` | 反序列化仅含 `name` 和 `base_url` 的 Ollama 提供商配置，验证默认字段值 |
| `test_deserialize_azure_model_provider_toml` | 反序列化含 `env_key` 和 `query_params` 的 Azure 提供商配置，验证查询参数正确解析 |
| `test_deserialize_example_model_provider_toml` | 反序列化含 `http_headers` 和 `env_http_headers` 的自定义提供商配置，验证自定义头和环境变量头的映射 |
| `test_deserialize_chat_wire_api_shows_helpful_error` | 当 TOML 中指定 `wire_api = "chat"` 时，反序列化应失败并返回包含 `CHAT_WIRE_API_REMOVED_ERROR` 的友好错误消息 |
| `test_deserialize_websocket_connect_timeout` | 反序列化含 `websocket_connect_timeout_ms` 和 `supports_websockets` 的配置，验证超时值正确解析 |
