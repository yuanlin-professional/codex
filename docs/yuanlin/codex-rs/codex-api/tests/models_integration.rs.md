# `models_integration.rs` — 测试模块

## 测试覆盖范围

使用 wiremock 模拟服务器验证 `ModelsClient` 的端到端集成行为。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `models_client_hits_models_endpoint` | 使用真实 HTTP transport 验证 GET /models 路径、参数和响应解析 |
