# `turn_timing.rs` — 回合时序指标（TTFT/TTFM）记录

## 功能概述

此文件实现了回合级别的时序指标记录，包括 TTFT（Time To First Token，首个令牌时间）和 TTFM（Time To First Message，首条消息时间）。它通过 `TurnTimingState` 跟踪回合开始时间和首次输出的时间戳，并将计算得到的延迟通过 OpenTelemetry 指标上报。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `TurnTimingState` | 结构体 | 回合时序状态，使用 Mutex 保护的内部状态 |
| `TurnTimingStateInner` | 结构体（私有） | 内部状态：started_at、first_token_at、first_message_at |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `record_turn_ttft_metric` | `async (turn_context, event)` | 记录 TTFT 指标 |
| `record_turn_ttfm_metric` | `async (turn_context, item)` | 记录 TTFM 指标 |
| `TurnTimingState::mark_turn_started` | `async (&self, started_at)` | 标记回合开始时间并重置首次记录 |
| `response_event_records_turn_ttft` | `(event) -> bool` | 判断响应事件是否应记录为首个令牌 |
| `response_item_records_turn_ttft` | `(item) -> bool` | 判断响应项是否应记录为首个令牌 |

## 依赖关系

- **引用的 crate**: `codex_otel`（指标名称常量）、`codex_protocol`（`TurnItem`、`ResponseItem`）、`tokio::sync::Mutex`
- **被引用方**: 回合执行流程使用此模块记录时序指标

## 实现备注

- TTFT 触发条件：文本 delta、推理摘要 delta、推理内容 delta、带内容的完成项等
- TTFM 仅由 `TurnItem::AgentMessage` 触发
- 每种指标在一个回合中只记录一次（`first_token_at`/`first_message_at` 不为 None 后跳过）
- 消息项只有在包含非空输出文本时才计为 TTFT
- 工具调用和搜索调用都计为 TTFT（它们表示模型开始产出）
- 工具输出和 `Other` 类型不计为 TTFT
