# `device_code_login.rs` — 测试模块

## 测试覆盖范围
使用 WireMock 模拟服务端，端到端验证设备码（device code）登录流程。覆盖成功登录、workspace 不匹配拒绝、HTTP 错误处理、无 API key 交换场景和错误负载处理。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `device_code_login_integration_succeeds` | 完整设备码登录成功流程，验证 auth.json 持久化 |
| `device_code_login_rejects_workspace_mismatch` | workspace ID 不匹配时拒绝登录 |
| `device_code_login_integration_handles_usercode_http_failure` | usercode 端点返回 503 时正确报错 |
| `device_code_login_integration_persists_without_api_key_on_exchange_failure` | API key 交换失败但 token 仍正确持久化 |
| `device_code_login_integration_handles_error_payload` | token 轮询返回 401 错误负载时正确报错 |
