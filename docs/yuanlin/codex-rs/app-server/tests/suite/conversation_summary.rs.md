# `conversation_summary.rs` — 测试模块

## 测试覆盖范围
测试 `getConversationSummary` JSON-RPC 方法，验证通过 thread ID 或 rollout 文件路径查询会话摘要的功能。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `get_conversation_summary_by_thread_id_reads_rollout` | 通过 thread ID 查询，返回 rollout 文件中的会话摘要 |
| `get_conversation_summary_by_relative_rollout_path_resolves_from_codex_home` | 通过相对 rollout 路径查询，基于 CODEX_HOME 解析并返回摘要 |
