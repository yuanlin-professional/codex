# `resume.rs` -- 测试模块

## 测试覆盖范围
测试 Codex 会话恢复（resume）功能，包括从 rollout 事件中恢复初始消息、恢复推理（reasoning）事件、在恢复时切换模型并保留基础指令，以及在 pre-turn override 后模型切换消息不被重复。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `resume_includes_initial_messages_from_rollout_events` | 恢复会话后，初始消息中包含用户消息、助手消息、token 计数及 turn 完成事件，并验证 text_elements 正确保留 |
| `resume_includes_initial_messages_from_reasoning_events` | 启用 `show_raw_agent_reasoning` 后恢复会话，初始消息中包含 AgentReasoning 和 AgentReasoningRawContent 事件 |
| `resume_switches_models_preserves_base_instructions` | 使用不同模型恢复会话时，base instructions 与初始会话一致，并在首次 post-resume turn 包含 `<model_switch>` 消息，后续 turn 不重复 |
| `resume_model_switch_is_not_duplicated_after_pre_turn_override` | 恢复后先执行 OverrideTurnContext 切换模型，再发送 turn，`<model_switch>` 消息仅出现一次 |
