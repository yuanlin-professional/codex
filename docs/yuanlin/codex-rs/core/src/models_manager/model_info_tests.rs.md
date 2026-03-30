# `model_info_tests.rs` -- 测试模块

## 测试覆盖范围

覆盖 `model_info.rs` 中 `with_config_overrides` 函数对 `supports_reasoning_summaries` 配置覆盖的行为。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `reasoning_summaries_override_true_enables_support` | 验证配置为 true 时启用推理摘要支持 |
| `reasoning_summaries_override_false_does_not_disable_support` | 验证配置为 false 时不会禁用已启用的推理摘要支持 |
| `reasoning_summaries_override_false_is_noop_when_model_is_false` | 验证模型本身不支持时配置为 false 无任何影响 |
