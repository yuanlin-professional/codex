# `exec_events.rs` — codex-exec JSONL 事件类型定义

## 功能概述

定义了 codex-exec 以 JSONL 格式输出的所有事件类型。这是面向外部消费者的公共 API，涵盖线程生命周期（started）、轮次生命周期（started/completed/failed）、以及各种 item 类型（代理消息、推理、命令执行、文件变更、MCP 工具调用、协作工具调用、Web 搜索、todo 列表、错误）。所有类型均派生 Serialize/Deserialize 和 TS（TypeScript 类型导出）。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ThreadEvent` | enum | 顶层 JSONL 事件枚举，使用 `#[serde(tag = "type")]` 区分 |
| `ThreadItem` | struct | 通用 item 容器，包含 id 和 `ThreadItemDetails` |
| `ThreadItemDetails` | enum | item 类型载荷：AgentMessage、Reasoning、CommandExecution、FileChange、McpToolCall、CollabToolCall、WebSearch、TodoList、Error |
| `Usage` | struct | token 用量：input_tokens、cached_input_tokens、output_tokens |
| `CommandExecutionItem` | struct | 命令执行详情：command、output、exit_code、status |
| `FileChangeItem` | struct | 文件变更列表和状态 |
| `McpToolCallItem` | struct | MCP 工具调用：server、tool、arguments、result、error、status |
| `CollabToolCallItem` | struct | 协作工具调用：tool 类型、发送/接收线程 ID、代理状态 |
| `WebSearchItem` | struct | Web 搜索请求：id、query、action |
| `TodoListItem` | struct | 代理 todo 列表的 items |

## 关键函数/方法

无独立函数，全部为数据类型定义。

## 依赖关系

- **引用的 crate**: `codex_protocol`、`serde`、`serde_json`、`ts_rs`
- **被引用方**: `event_processor_with_jsonl_output.rs`、外部 TypeScript/JSON 消费者

## 实现备注

- `McpToolCallItemResult` 使用 `serde_json::Value` 而非 rmcp 具体类型，以保持 schema/TS 友好性。
- 所有枚举使用 `rename_all = "snake_case"` 保持 JSON 字段风格一致。
