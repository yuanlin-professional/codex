# `permissions_messages.rs` — 测试模块

## 测试覆盖范围
权限消息的生成、更新、恢复与分叉行为，验证权限指令在请求中的注入、变更追踪、跨会话恢复以及可写根目录的包含等。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `permissions_message_sent_once_on_start` | 启动时发送一次权限消息 |
| `permissions_message_added_on_override_change` | 覆盖审批策略后在第二次请求中追加新的权限消息 |
| `permissions_message_not_added_when_no_change` | 未变更权限时不追加新的权限消息 |
| `resume_replays_permissions_messages` | 恢复会话时重放之前的权限消息历史 |
| `resume_and_fork_append_permissions_messages` | 恢复和分叉会话时正确追加新的权限消息 |
| `permissions_message_includes_writable_roots` | 权限消息包含配置的可写根目录信息 |
