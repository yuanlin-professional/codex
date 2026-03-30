# `session_prefix.rs` — 子代理通知消息格式化

该文件提供两个辅助函数，用于格式化模型可见的会话状态标记。`format_subagent_notification_message` 将代理引用路径和状态序列化为 JSON，并使用 `SUBAGENT_NOTIFICATION_FRAGMENT` 标记包装，生成嵌入在用户角色消息中的子代理通知。`format_subagent_context_line` 生成子代理上下文行，格式为 `"- {agent_reference}: {agent_nickname}"` 或仅 `"- {agent_reference}"`（当昵称为空时）。
