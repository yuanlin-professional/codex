# `login_server_e2e.rs` — 测试模块

## 测试覆盖范围
端到端验证本地 OAuth 回调服务器登录流程。使用 tiny_http mock issuer 模拟 OAuth token 端点，通过 HTTP 请求模拟浏览器回调。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `end_to_end_login_flow_persists_auth_json` | 完整 OAuth 回调流程，验证 auth.json 正确写入 |
| `creates_missing_codex_home_dir` | codex_home 目录不存在时自动创建 |
| `forced_chatgpt_workspace_id_mismatch_blocks_login` | workspace 不匹配时阻止登录并报告错误 |
| `oauth_access_denied_missing_entitlement_blocks_login_with_clear_error` | Codex 权限缺失时显示明确错误信息 |
| `oauth_access_denied_unknown_reason_uses_generic_error_page` | 未知 OAuth 拒绝原因使用通用错误页面 |
| `cancels_previous_login_server_when_port_is_in_use` | 端口占用时取消前一个登录服务器并接管 |
