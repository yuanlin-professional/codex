# `subagent_notifications.rs` -- 测试模块

## 测试覆盖范围
测试子代理（subagent）系统的通知和上下文 fork 功能，包括子代理通知在后续 turn 中的注入、fork 上下文的传递、spawn_agent 参数对模型/推理级别的覆盖、agent_type/role 配置覆盖，以及 spawn_agent 工具描述中对 role 锁定设置的展示。依赖 Collab feature 标志。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `subagent_notification_is_included_without_wait` | 子代理完成后，父代理后续 turn 请求中包含 `<subagent_notification>` |
| `spawned_child_receives_forked_parent_context` | fork_context=true 时子代理请求中包含父代理历史和特定的 fork output 消息 |
| `spawn_agent_requested_model_and_reasoning_override_inherited_settings_without_role` | spawn_agent 显式指定 model 和 reasoning_effort 时覆盖继承值 |
| `spawn_agent_role_overrides_requested_model_and_reasoning_settings` | agent_type 指定的 role 配置覆盖 spawn_agent 的 model 和 reasoning_effort |
| `spawn_agent_tool_description_mentions_role_locked_settings` | spawn_agent 工具描述中展示 role 锁定的模型和推理级别 |
