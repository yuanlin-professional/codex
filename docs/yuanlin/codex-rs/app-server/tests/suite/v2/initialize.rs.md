# `initialize.rs` — 测试模块

## 测试覆盖范围
测试 `initialize` JSON-RPC 方法的行为，包括客户端名称作为 originator、
环境变量覆盖 originator、无效客户端名称拒绝、通知方法过滤（opt-out）
以及 turn 启动通知中包含客户端名称。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `initialize_uses_client_info_name_as_originator` | 客户端名称用作 originator 标识 |
| `initialize_respects_originator_override_env_var` | 环境变量可覆盖 originator |
| `initialize_rejects_invalid_client_name` | 无效的客户端名称被拒绝 |
| `initialize_opt_out_notification_methods_filters_notifications` | opt-out 的通知方法不被发送 |
| `turn_start_notify_payload_includes_initialize_client_name` | turn 启动通知包含初始化时的客户端名称 |
