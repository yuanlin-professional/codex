# `sqlite_state.rs` — 测试模块

## 测试覆盖范围

SQLite 状态持久化功能的集成测试，覆盖线程元数据记录、rollout 回填扫描、用户消息持久化、Web 搜索和 MCP 工具调用对内存模式（memory mode）的污染标记，以及工具调用日志中 thread_id 的传播。所有测试都依赖 `Feature::Sqlite` 特性标志。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `new_thread_is_recorded_in_state_db` | 验证新线程在首次用户消息后被记录到 SQLite 状态数据库中，且 rollout 路径正确 |
| `backfill_scans_existing_rollouts` | 验证启动时对已有 rollout 文件的回填扫描，确认线程元数据和动态工具（dynamic tools）被正确存储 |
| `user_messages_persist_in_state_db` | 验证多轮用户消息后 `first_user_message` 字段被正确持久化 |
| `web_search_marks_thread_memory_mode_polluted_when_configured` | 当配置 `no_memories_if_mcp_or_web_search` 时，Web 搜索调用会将线程内存模式标记为 "polluted" |
| `mcp_call_marks_thread_memory_mode_polluted_when_configured` | 当配置 `no_memories_if_mcp_or_web_search` 时，MCP 工具调用会将线程内存模式标记为 "polluted" |
| `tool_call_logs_include_thread_id` | 验证工具调用日志行（ToolCall）中正确包含 thread_id 字段 |
