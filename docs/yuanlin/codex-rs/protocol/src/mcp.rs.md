# `mcp.rs` — Model Context Protocol (MCP) 类型定义

## 功能概述

定义了在 Codex 协议中表示 MCP（Model Context Protocol）值的所有类型，包括请求 ID、工具定义、资源描述、资源模板和工具调用结果。这些类型均通过 `ts-rs` 和 `schemars` 保持 TypeScript/JSON Schema 友好。还提供了从 MCP wire-format JSON（通常来自 rmcp 模型结构体）到协议类型的适配转换层。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `RequestId` | 枚举 | MCP 请求 ID，可为字符串或整数 |
| `Tool` | 结构体 | 工具定义（名称、标题、描述、输入/输出 Schema、注解、图标、元数据） |
| `Resource` | 结构体 | 已知可读资源（URI、名称、MIME 类型、大小等） |
| `ResourceTemplate` | 结构体 | 资源模板（URI 模板 + 元数据） |
| `CallToolResult` | 结构体 | 工具调用结果（内容数组、结构化内容、是否错误） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `Tool::from_mcp_value` | `fn from_mcp_value(value: Value) -> Result<Self, Error>` | 从 MCP JSON 值反序列化为 Tool |
| `Resource::from_mcp_value` | `fn from_mcp_value(value: Value) -> Result<Self, Error>` | 从 MCP JSON 值反序列化为 Resource |
| `ResourceTemplate::from_mcp_value` | `fn from_mcp_value(value: Value) -> Result<Self, Error>` | 从 MCP JSON 值反序列化为 ResourceTemplate |

## 依赖关系

- **引用的 crate**: `schemars`, `serde`, `serde_json`, `ts_rs`
- **被引用方**: `protocol.rs` 中的 MCP 事件，`app-server-protocol` 中的 MCP 相关通知/请求

## 实现备注

- 使用内部 `*Serde` 中间结构体进行宽松反序列化，支持 camelCase 和 snake_case 别名。
- `Resource.size` 使用自定义反序列化器 `deserialize_lossy_opt_i64` 处理超过 i64 范围的数值（返回 None 而非报错）。
- 文件共 329 行，包含完整的 MCP 类型体系和适配转换。
