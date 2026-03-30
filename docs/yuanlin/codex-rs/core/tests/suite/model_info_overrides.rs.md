# `model_info_overrides.rs` — 测试模块

## 测试覆盖范围
模型信息覆盖功能，验证离线模型信息中截断策略在有无 tool_output_token_limit 配置时的行为。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `offline_model_info_without_tool_output_override` | 未设置 tool_output_token_limit 时，截断策略使用默认字节限制 |
| `offline_model_info_with_tool_output_override` | 设置 tool_output_token_limit 后，截断策略使用指定的 token 限制 |
