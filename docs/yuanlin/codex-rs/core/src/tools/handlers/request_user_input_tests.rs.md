# `request_user_input_tests.rs` — 测试模块

## 测试覆盖范围
测试覆盖了 `request_user_input` 工具的模式可用性判断逻辑和工具描述生成功能，验证各协作模式（Plan、Default、Execute、PairProgramming）下的权限控制以及 feature flag 对可用性的影响。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `request_user_input_mode_availability_defaults_to_plan_only` | 验证默认情况下仅 Plan 模式允许 `request_user_input`，其他模式（Default、Execute、PairProgramming）不允许 |
| `request_user_input_unavailable_messages_respect_default_mode_feature_flag` | 验证 `request_user_input_unavailable_message` 在各模式下返回正确的消息：Plan 模式返回 None，Default 模式根据 feature flag 决定，Execute/PairProgramming 模式始终返回不可用消息 |
| `request_user_input_tool_description_mentions_available_modes` | 验证工具描述文本正确反映可用模式：关闭 flag 时提及 "Plan mode"，开启时提及 "Default or Plan mode" |
