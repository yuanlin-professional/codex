# `collaboration_mode_presets_tests.rs` -- 测试模块

## 测试覆盖范围

覆盖 `collaboration_mode_presets.rs` 中的协作模式预设构建和模板渲染逻辑。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `preset_names_use_mode_display_names` | 验证 Plan 和 Default 预设名称匹配 ModeKind 的 display_name，Plan 推理强度为 Medium |
| `default_mode_instructions_replace_mode_names_placeholder` | 验证 Default 模式指令中所有模板占位符被正确替换，且启用 request_user_input 时包含相应指导 |
| `default_mode_instructions_use_plain_text_questions_when_feature_disabled` | 验证禁用 request_user_input 时 Default 模式使用纯文本提问指导 |
