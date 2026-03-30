# `description.rs` -- Code Mode 工具描述与 exec 解析

## 功能概述

本模块负责 Code Mode 的工具描述生成、exec 源码解析和 JSON Schema 到 TypeScript 类型转换。它定义了 `exec` 和 `wait` 工具的描述模板，解析 exec 输入中的 pragma 注释（`// @exec: {...}`）以提取 `yield_time_ms` 和 `max_output_tokens` 参数，并为嵌套工具生成 TypeScript 类型声明以便在 JavaScript 执行环境中使用。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `CodeModeToolKind` | enum | 工具类型：`Function`（结构化输入）或 `Freeform`（自由文本输入） |
| `ToolDefinition` | struct | 工具定义：名称、描述、类型、输入/输出 schema |
| `ParsedExecSource` | struct | 解析后的 exec 源码：代码、yield 超时、最大输出 token 数 |
| `EnabledToolMetadata` | struct | 启用工具的元数据：工具名、全局标识符名、描述、类型 |
| `CodeModeExecPragma` | struct | exec pragma 解析结果 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `parse_exec_source` | `pub fn parse_exec_source(input: &str) -> Result<ParsedExecSource, String>` | 解析 exec 输入，提取 pragma 参数和实际 JavaScript 源码 |
| `build_exec_tool_description` | `pub fn build_exec_tool_description(enabled_tools, code_mode_only) -> String` | 构建 exec 工具的完整描述，可选包含嵌套工具参考 |
| `normalize_code_mode_identifier` | `pub fn normalize_code_mode_identifier(tool_key: &str) -> String` | 将工具名转换为合法的 JavaScript 标识符 |
| `augment_tool_definition` | `pub fn augment_tool_definition(definition) -> ToolDefinition` | 为工具定义追加 TypeScript 类型声明 |
| `render_json_schema_to_typescript` | `pub fn render_json_schema_to_typescript(schema: &JsonValue) -> String` | 将 JSON Schema 转换为 TypeScript 类型字符串 |

## 依赖关系

- **引用的 crate**: `serde`, `serde_json`
- **被引用方**: `lib.rs`（公开导出）, `runtime/mod.rs`, `runtime/globals.rs`

## 实现备注

- JSON Schema 到 TypeScript 转换支持 `const`、`enum`、`anyOf`/`oneOf`/`allOf`、`object`（含 `additionalProperties`）、`array`（含 `prefixItems`/`items`）
- 无效的标识符字符被替换为下划线，确保生成合法的 JavaScript 属性名
- code_mode_only 模式下，exec 工具描述会包含所有嵌套工具的完整参考文档
