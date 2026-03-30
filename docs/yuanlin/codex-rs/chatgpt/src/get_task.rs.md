# `get_task.rs` — 获取 Codex 云端任务响应

## 功能概述
定义用于反序列化 Codex 云端任务 API 响应的数据结构，并提供 `get_task` 函数通过 ChatGPT 后端 API 获取指定任务的详情。响应中包含 diff 输出所在的 assistant turn。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `GetTaskResponse` | 结构体 | 任务响应顶层，包含 current_diff_task_turn |
| `AssistantTurn` | 结构体 | 助手回合，包含 output_items 列表 |
| `OutputItem` | 枚举 | 输出项：Pr（含 diff）或 Other |
| `PrOutputItem` | 结构体 | PR 类型输出，包含 OutputDiff |
| `OutputDiff` | 结构体 | 包含 diff 字符串 |

## 依赖关系
- **引用的 crate**: `codex_core`, `serde`
- **被引用方**: `apply_command.rs`, 集成测试
