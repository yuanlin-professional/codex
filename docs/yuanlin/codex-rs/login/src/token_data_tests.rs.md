# `token_data_tests.rs` — 测试模块

## 测试覆盖范围
验证 JWT token 解析逻辑，覆盖 email/plan type 提取、各种计划类型识别、缺失字段处理、JWT 过期时间解析和 workspace 账户检测。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `id_token_info_parses_email_and_plan` | 正确提取 email 和 Pro 计划 |
| `id_token_info_parses_go_plan` | 正确识别 Go 计划 |
| `id_token_info_parses_hc_plan_as_enterprise` | hc 映射为 Enterprise |
| `id_token_info_parses_usage_based_business_plans` | 识别 usage-based 商业计划 |
| `id_token_info_handles_missing_fields` | 缺失字段返回 None |
| `jwt_expiration_parses_exp_claim` | 正确解析 exp 过期声明 |
| `workspace_account_detection_matches_workspace_plans` | 正确区分个人和 workspace 账户 |
