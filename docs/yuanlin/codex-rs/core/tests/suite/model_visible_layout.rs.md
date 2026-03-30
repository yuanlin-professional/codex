# `model_visible_layout.rs` — 测试模块

## 测试覆盖范围
模型可见布局快照测试，验证请求的输入布局在不同场景下的结构正确性，包括轮次覆盖、cwd 变更、恢复后个性变更以及子代理环境上下文等。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `snapshot_model_visible_layout_turn_overrides` | 第二轮变更 cwd、审批策略和个性时的请求布局快照 |
| `snapshot_model_visible_layout_cwd_change_does_not_refresh_agents` | cwd 变更到不同 AGENTS.md 目录时当前行为不刷新 AGENTS 指令 |
| `snapshot_model_visible_layout_resume_with_personality_change` | 恢复后首轮变更模型和个性时的请求布局快照 |
| `snapshot_model_visible_layout_resume_override_matches_rollout_model` | 恢复后模型覆盖设为 rollout 原始模型时不出现 model-switch 更新 |
| `snapshot_model_visible_layout_environment_context_includes_one_subagent` | 环境上下文包含单个子代理的快照 |
| `snapshot_model_visible_layout_environment_context_includes_two_subagents` | 环境上下文包含两个子代理的快照 |
