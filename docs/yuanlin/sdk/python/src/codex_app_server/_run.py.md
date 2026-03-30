# `_run.py` -- Turn 运行结果收集与响应提取

## 功能概述

本模块实现了从通知事件流中收集 turn 运行结果的核心逻辑。它定义了 `RunResult` 数据类作为便捷 `run()` 调用的返回值，并提供同步和异步两个版本的结果收集函数。模块还包含从 `ThreadItem` 列表中提取最终助手响应文本的辅助逻辑，支持 `final_answer` 阶段优先和无阶段标记的回退策略。

## 关键类/函数

| 名称 | 类型 | 说明 |
|------|------|------|
| `RunResult` | 数据类 | run 结果容器，包含 `final_response`、`items` 和 `usage` |
| `_collect_run_result()` | 内部函数 | 从同步通知流中收集指定 turn 的完整结果 |
| `_collect_async_run_result()` | 内部函数 | 从异步通知流中收集指定 turn 的完整结果 |
| `_final_assistant_response_from_items()` | 内部函数 | 从 item 列表中提取最终助手响应文本 |
| `_agent_message_item_from_thread_item()` | 内部函数 | 从 ThreadItem 中提取 AgentMessageThreadItem |
| `_raise_for_failed_turn()` | 内部函数 | 若 turn 状态为 failed 则抛出 RuntimeError |

## 依赖关系

- **引用的模块**: `.generated.v2_all`（通知类型、Turn 类型等）, `.models`（`Notification`）
- **被引用方**: `.api`（`Thread.run()` 和 `AsyncThread.run()` 调用）

## 实现备注

- `final_response` 的提取策略：优先选取 `phase == final_answer` 的消息，若不存在则回退到最后一个无阶段标记的助手消息
- 若 turn 完成事件未收到，则抛出 `RuntimeError`
- 同步和异步收集函数逻辑完全对称，仅在迭代方式上不同（`for` vs `async for`）
