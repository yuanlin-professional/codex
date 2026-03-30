# `test_public_api_runtime_behavior.py` -- 测试模块

## 测试覆盖范围

验证公共 API 层（`Codex`、`AsyncCodex`、`Thread`、`TurnHandle` 等）的运行时行为，使用 mock 替换底层客户端来测试初始化流程、turn 流消费、run 结果收集和并发约束等逻辑。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `test_codex_init_failure_closes_client` | 验证 Codex 初始化失败时自动关闭底层客户端 |
| `test_async_codex_init_failure_closes_client` | 验证 AsyncCodex 初始化失败时自动关闭底层客户端并重置状态 |
| `test_async_codex_initializes_only_once_under_concurrency` | 验证 AsyncCodex 在并发调用下只初始化一次 |
| `test_turn_stream_rejects_second_active_consumer` | 验证同步 turn 流拒绝第二个活跃消费者 |
| `test_async_turn_stream_rejects_second_active_consumer` | 验证异步 turn 流拒绝第二个活跃消费者 |
| `test_turn_run_returns_completed_turn_payload` | 验证 TurnHandle.run() 返回正确的完成载荷 |
| `test_thread_run_accepts_string_input_and_returns_run_result` | 验证 Thread.run() 接受字符串输入并返回 RunResult |
| `test_thread_run_uses_last_completed_assistant_message_as_final_response` | 验证多消息时使用最后一条助手消息作为 final_response |
| `test_thread_run_preserves_empty_last_assistant_message` | 验证空文本的最后助手消息被保留 |
| `test_thread_run_prefers_explicit_final_answer_over_later_commentary` | 验证 final_answer 阶段消息优先于后续 commentary |
| `test_thread_run_returns_none_when_only_commentary_messages_complete` | 验证只有 commentary 消息时 final_response 为 None |
| `test_thread_run_raises_on_failed_turn` | 验证 turn 失败时抛出 RuntimeError |
| `test_async_thread_run_accepts_string_input_and_returns_run_result` | 验证 AsyncThread.run() 接受字符串输入并返回 RunResult |
| `test_async_thread_run_uses_last_completed_assistant_message_as_final_response` | 验证异步多消息时使用最后一条助手消息 |
| `test_async_thread_run_returns_none_when_only_commentary_messages_complete` | 验证异步只有 commentary 时 final_response 为 None |
| `test_retry_examples_compare_status_with_enum` | 验证重试示例使用枚举比较而非字符串比较 |
