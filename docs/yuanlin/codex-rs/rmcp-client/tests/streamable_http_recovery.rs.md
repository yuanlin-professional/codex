# `streamable_http_recovery.rs` -- 测试模块

## 测试覆盖范围
验证 `RmcpClient` 在 Streamable HTTP 传输模式下遇到会话过期（404）、认证失败（401）和服务器错误（500）等异常时的恢复行为，确保仅对 404 会话过期进行自动重连重试，且最多重试一次。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `streamable_http_404_session_expiry_recovers_and_retries_once` | 注入一次 404 失败后，客户端自动恢复会话并成功执行后续工具调用 |
| `streamable_http_401_does_not_trigger_recovery` | 注入 401 失败后，客户端不触发会话恢复，连续请求均返回 401 错误 |
| `streamable_http_404_recovery_only_retries_once` | 注入两次连续 404 失败，验证客户端仅重试一次后报错，后续请求恢复正常 |
| `streamable_http_non_session_failure_does_not_trigger_recovery` | 注入 500 服务器错误后，客户端不触发会话恢复，连续请求均返回 500 错误 |
