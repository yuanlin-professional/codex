# `context.rs` — 工具调用上下文与输出类型

## 功能概述
定义了工具系统的核心上下文类型和输出 trait。包括工具调用的载荷类型（Function、ToolSearch、Custom、LocalShell、Mcp）、多种输出实现（FunctionToolOutput、ApplyPatchToolOutput、ExecCommandToolOutput 等）以及遥测预览生成逻辑。这是工具系统中类型定义最集中的文件。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ToolInvocation` | struct | 工具调用上下文：session、turn、tracker、call_id、tool_name、payload |
| `ToolPayload` | enum | 载荷类型：Function/ToolSearch/Custom/LocalShell/Mcp |
| `ToolCallSource` | enum | 调用来源：Direct/JsRepl/CodeMode |
| `FunctionToolOutput` | struct | 通用函数工具输出：body + success + post_tool_use_response |
| `ApplyPatchToolOutput` | struct | 补丁工具输出 |
| `ExecCommandToolOutput` | struct | unified exec 输出：raw_output、wall_time、exit_code、chunk_id 等 |
| `AbortedToolOutput` | struct | 中止的工具输出 |
| `ToolSearchOutput` | struct | 工具搜索输出 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ToolOutput::to_response_item` | `fn(&self, call_id, payload) -> ResponseInputItem` | 将输出转为协议响应项 |
| `ToolOutput::code_mode_result` | `fn(&self, payload) -> JsonValue` | 将输出转为 Code Mode 友好的 JSON 值 |
| `telemetry_preview` | `fn(content) -> String` | 生成遥测预览文本（限制字节和行数） |
| `function_tool_response` | `fn(call_id, payload, body, success) -> ResponseInputItem` | 构建函数工具响应，根据 payload 类型选择 Function/Custom 输出 |
| `response_input_to_code_mode_result` | `fn(ResponseInputItem) -> JsonValue` | 将 ResponseInputItem 转为 Code Mode JSON 结果 |

## 依赖关系
- **引用的 crate**: `codex_protocol`, `codex_utils_output_truncation`, `codex_utils_string`, `serde_json`
- **被引用方**: 几乎所有工具处理器和运行时模块

## 实现备注
- `ToolPayload::Custom` 用于 freeform 工具，其响应映射为 `CustomToolCallOutput`
- `ExecCommandToolOutput` 的 `truncated_output()` 使用 token 级别截断
- `telemetry_preview` 限制为 2KiB、64 行
- `SharedTurnDiffTracker` 为 `Arc<Mutex<TurnDiffTracker>>` 类型别名
