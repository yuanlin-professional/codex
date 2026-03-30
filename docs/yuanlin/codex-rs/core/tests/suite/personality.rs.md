# `personality.rs` — 测试模块

## 测试覆盖范围
个性（Personality）功能，包括个性模板注入、个性变更更新消息、特性开关控制、远程模型模板支持以及默认个性行为等。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `personality_does_not_mutate_base_instructions_without_template` | 无模板时个性设置不修改基础指令 |
| `base_instructions_override_disables_personality_template` | base_instructions 覆盖时禁用个性模板 |
| `user_turn_personality_none_does_not_add_update_message` | 个性为 None 时不添加更新消息 |
| `config_personality_some_sets_instructions_template` | 配置个性后正确设置指令模板 |
| `config_personality_none_sends_no_personality` | 配置 Personality::None 时不发送个性模板 |
| `default_personality_is_pragmatic_without_config_toml` | 无 config.toml 时默认个性为 Pragmatic |
| `user_turn_personality_some_adds_update_message` | 个性变更时在 developer 消息中添加个性更新消息 |
| `user_turn_personality_same_value_does_not_add_update_message` | 个性值未变更时不添加更新消息 |
| `instructions_uses_base_if_feature_disabled` | Personality 特性禁用时使用基础指令 |
| `user_turn_personality_skips_if_feature_disabled` | Personality 特性禁用时跳过个性更新 |
| `remote_model_friendly_personality_instructions_with_feature` | 远程模型使用 friendly 个性时应用远程模板中的友好变体 |
| `user_turn_personality_remote_model_template_includes_update_message` | 远程模型个性变更时在 developer 消息中包含远程模板的更新消息 |
