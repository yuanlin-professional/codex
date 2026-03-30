# `turn_start.rs` — 测试模块

## 测试覆盖范围
全面测试 `turn/start` JSON-RPC 方法的各种功能，包括 originator 头发送、文本元素通知、
输入大小限制验证、通知发送和模型覆盖、协作模式覆盖、人格覆盖、图片输入、
命令执行审批（批准/拒绝）、沙箱和 cwd 更新、文件变更审批（批准/会话级持久化/拒绝）、
子代理 spawn item 通知（含模型元数据和角色元数据），以及命令执行 process_id。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `turn_start_sends_originator_header` | 发送 originator 请求头 |
| `turn_start_emits_user_message_item_with_text_elements` | 发送包含文本元素的用户消息 item |
| `turn_start_accepts_text_at_limit_with_mention_item` | 接受限制范围内含 mention 的文本 |
| `turn_start_rejects_combined_oversized_text_input` | 拒绝超大文本输入 |
| `turn_start_emits_notifications_and_accepts_model_override` | 发送通知并接受模型覆盖 |
| `turn_start_accepts_collaboration_mode_override_v2` | 接受协作模式覆盖 |
| `turn_start_uses_thread_feature_overrides_for_collaboration_mode_instructions_v2` | 使用线程特性覆盖的协作模式指令 |
| `turn_start_accepts_personality_override_v2` | 接受人格覆盖 |
| `turn_start_change_personality_mid_thread_v2` | 线程中途更改人格 |
| `turn_start_uses_migrated_pragmatic_personality_without_override_v2` | 使用迁移的 pragmatic 人格 |
| `turn_start_accepts_local_image_input` | 接受本地图片输入 |
| `turn_start_exec_approval_toggle_v2` | 命令执行审批切换 |
| `turn_start_exec_approval_decline_v2` | 命令执行审批拒绝 |
| `turn_start_updates_sandbox_and_cwd_between_turns_v2` | turns 之间更新沙箱和 cwd |
| `turn_start_file_change_approval_v2` | 文件变更审批流程 |
| `turn_start_emits_spawn_agent_item_with_model_metadata_v2` | 发送含模型元数据的子代理 spawn item |
| `turn_start_emits_spawn_agent_item_with_effective_role_model_metadata_v2` | 发送含有效角色模型元数据的子代理 spawn item |
| `turn_start_file_change_approval_accept_for_session_persists_v2` | 会话级文件变更审批持久化 |
| `turn_start_file_change_approval_decline_v2` | 文件变更审批拒绝 |
| `command_execution_notifications_include_process_id` | 命令执行通知包含 process_id |
