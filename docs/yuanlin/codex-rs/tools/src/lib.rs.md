# `lib.rs` -- codex-tools crate 入口

## 功能概述
codex-tools crate 的库入口文件，组织所有工具定义子模块并统一导出公共 API。提供 Codex 使用的全部工具定义创建函数（shell 命令、agent 管理、MCP 工具/资源、视图图像、JavaScript REPL、代码模式、Web 搜索等），以及 JSON Schema 解析和 Responses API 序列化功能。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ToolSpec` | re-export | 工具规格枚举 |
| `JsonSchema` | re-export | JSON Schema 子集枚举 |
| `ToolDefinition` | re-export | 工具定义结构体 |
| `ResponsesApiTool` | re-export | Responses API 工具格式 |

## 依赖关系
- **被引用方**: codex-core 构建工具集
