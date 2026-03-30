# `prompt_caching.rs` — 测试模块

## 测试覆盖范围
提示缓存（Prompt Caching）功能，验证请求间的工具列表、指令、缓存前缀和缓存键的一致性，以及轮次覆盖和上下文变更时的行为。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `prompt_tools_are_consistent_across_requests` | 跨请求的工具列表和指令保持一致 |
| `gpt_5_tools_without_apply_patch_append_apply_patch_instructions` | 禁用 ApplyPatchFreeform 时 apply_patch 指令追加到基础指令中 |
| `prefixes_context_and_instructions_once_and_consistently_across_requests` | 上下文和指令前缀一次性注入且跨请求保持一致 |
| `overrides_turn_context_but_keeps_cached_prefix_and_key_constant` | 覆盖轮次上下文时缓存前缀和缓存键保持不变 |
| `override_before_first_turn_emits_environment_context` | 首轮之前覆盖上下文时正确发射环境上下文 |
| `per_turn_overrides_keep_cached_prefix_and_key_constant` | 逐轮覆盖时缓存前缀和缓存键保持不变 |
| `send_user_turn_with_no_changes_does_not_send_environment_context` | 无变更的 UserTurn 不发送额外环境上下文 |
| `send_user_turn_with_changes_sends_environment_context` | 有变更的 UserTurn 发送更新的环境上下文和权限消息 |
