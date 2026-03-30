# `fork_thread.rs` — 测试模块

## 测试覆盖范围

测试对话线程分叉(Fork Thread)功能，验证连续两次分叉操作后 rollout 文件中保留的对话项(RolloutItem)是否正确截断到预期的用户消息位置。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `fork_thread_twice_drops_to_first_message` | 发送三条用户消息后，第一次分叉截断到第二条消息前，第二次分叉截断到第一条消息前，验证 rollout 文件内容的正确性 |
