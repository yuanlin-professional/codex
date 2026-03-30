# `realtime_conversation.rs` — 测试模块

## 测试覆盖范围
测试实时对话（realtime conversation）功能的 v2 通知流、停止后关闭通知发送，
以及特性标志门控。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `realtime_conversation_streams_v2_notifications` | 实时对话流式推送 v2 通知 |
| `realtime_conversation_stop_emits_closed_notification` | 停止实时对话发送关闭通知 |
| `realtime_conversation_requires_feature_flag` | 实时对话需要特性标志启用 |
