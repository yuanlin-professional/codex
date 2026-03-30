# `turn_timing_tests.rs` — 测试模块

## 测试覆盖范围

测试对话轮次计时（turn timing）状态机的行为，包括首次 token 时间（TTFT）和首次消息时间（TTFM）的记录逻辑，以及 `response_item_records_turn_ttft` 函数对不同类型 `ResponseItem` 的判定。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `turn_timing_state_records_ttft_only_once_per_turn` | TTFT 仅在轮次开始后首次收到文本增量时记录一次，后续增量不再记录；轮次未开始时返回 `None` |
| `turn_timing_state_records_ttfm_independently_of_ttft` | TTFM 独立于 TTFT 记录，首次 `AgentMessage` 触发记录，第二次则返回 `None` |
| `response_item_records_turn_ttft_for_first_output_signals` | `FunctionCall`、`CustomToolCall` 和包含非空文本的 `Message` 均被识别为有效的 TTFT 触发信号 |
| `response_item_records_turn_ttft_ignores_empty_non_output_items` | 空文本消息和 `FunctionCallOutput` 不被视为 TTFT 触发信号 |
