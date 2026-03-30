# `collaboration_instructions.rs` — 测试模块

## 测试覆盖范围

测试协作指令(Collaboration Instructions)功能，包括默认无协作指令、通过 OverrideTurnContext 注入协作指令、UserTurn 中携带协作指令、指令的覆盖与更新、指令不变时的去重(不重复追加)、模式(mode)切换时的指令更新、会话恢复(resume)后的指令重放，以及空字符串指令的忽略。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `no_collaboration_instructions_by_default` | 默认情况下不包含协作指令 XML 标签 |
| `user_input_includes_collaboration_instructions_after_override` | 通过 OverrideTurnContext 设置后，UserInput 请求中包含协作指令 |
| `collaboration_instructions_added_on_user_turn` | 通过 UserTurn 操作直接携带协作指令 |
| `override_then_next_turn_uses_updated_collaboration_instructions` | OverrideTurnContext 后下一轮使用更新的协作指令 |
| `user_turn_overrides_collaboration_instructions_after_override` | UserTurn 中的协作指令覆盖之前通过 Override 设置的指令 |
| `collaboration_mode_update_emits_new_instruction_message` | 更新协作模式指令时在历史中追加新的指令消息 |
| `collaboration_mode_update_noop_does_not_append` | 协作指令未变更时不重复追加 |
| `collaboration_mode_update_emits_new_instruction_message_when_mode_changes` | 模式(Default->Plan)变更时追加新指令消息 |
| `collaboration_mode_update_noop_does_not_append_when_mode_is_unchanged` | 模式和指令均未变更时不重复追加 |
| `resume_replays_collaboration_instructions` | 恢复会话后正确重放协作指令 |
| `empty_collaboration_instructions_are_ignored` | 空字符串的协作指令被忽略，不插入 XML 标签 |
