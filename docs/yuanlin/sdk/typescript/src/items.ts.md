# `items.ts` -- 线程条目类型定义

## 功能概述

`items.ts` 定义了 Codex 代理对话线程中所有可能出现的条目（Item）类型。这些类型对应 `codex-rs/exec/src/exec_events.rs` 中的 Rust 结构体，涵盖代理消息、推理摘要、命令执行、文件变更、MCP 工具调用、Web 搜索、待办列表和错误等。每种条目都有唯一 `id` 和 `type` 判别字段。

## 关键类型/接口

| 名称 | 类型 | 说明 |
|------|------|------|
| `CommandExecutionItem` | type | 命令执行条目，包含命令内容、聚合输出、退出码和状态 |
| `CommandExecutionStatus` | type | 命令状态枚举：`in_progress`、`completed`、`failed` |
| `FileChangeItem` | type | 文件变更条目，包含变更列表和补丁应用状态 |
| `FileUpdateChange` | type | 单个文件变更，含路径和变更类型（add/delete/update） |
| `PatchChangeKind` | type | 变更类型枚举 |
| `PatchApplyStatus` | type | 补丁应用状态 |
| `McpToolCallItem` | type | MCP 工具调用条目，含服务器名、工具名、参数和结果 |
| `AgentMessageItem` | type | 代理消息条目，包含文本响应 |
| `ReasoningItem` | type | 推理摘要条目 |
| `WebSearchItem` | type | Web 搜索条目，包含查询内容 |
| `ErrorItem` | type | 非致命错误条目 |
| `TodoListItem` | type | 待办列表条目，包含待办事项数组 |
| `TodoItem` | type | 单个待办事项，含文本和完成状态 |
| `ThreadItem` | union type | 所有条目类型的联合类型 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| 无 | -- | 此文件仅包含类型定义 |

## 依赖关系

- **引用的模块**: `@modelcontextprotocol/sdk/types.js`（McpContentBlock）
- **被引用方**: `./events.ts`、`./thread.ts`、`./index.ts`

## 实现备注

- `McpToolCallItem` 的 `result` 字段使用了 MCP SDK 的 `ContentBlock` 类型，是与 MCP 协议的集成点。
- `ThreadItem` 联合类型使用 `type` 字段作为判别属性，支持 TypeScript 的模式匹配式类型窄化。
- 所有条目都有 `id` 字段用于唯一标识。
