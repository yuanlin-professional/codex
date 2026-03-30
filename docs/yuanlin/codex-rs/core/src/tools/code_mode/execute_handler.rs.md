# `execute_handler.rs` — Code Mode 执行处理器

## 功能概述
该文件实现了 `CodeModeExecuteHandler` 结构体，作为 Code Mode 工具的执行入口。它接收来自模型的 JavaScript 源码文本，解析执行参数（包括 pragma 指令），调用 `CodeModeService` 执行代码，并将运行时响应转换为工具输出返回给模型。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `CodeModeExecuteHandler` | struct | Code Mode 执行处理器，实现了 `ToolHandler` trait |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `execute` | `async fn execute(&self, session, turn, call_id, code) -> Result<FunctionToolOutput, FunctionCallError>` | 核心执行逻辑：解析源码、构建可用工具列表、获取存储值、调用 code_mode_service 执行并处理响应 |
| `handle` | `async fn handle(&self, invocation) -> Result<Self::Output, FunctionCallError>` | `ToolHandler` trait 的实现，从 `ToolPayload::Custom` 中提取输入并委托给 `execute` |

## 依赖关系
- **引用的 crate**: `async_trait`, `codex_code_mode`
- **内部依赖**: `super::ExecContext`, `super::build_enabled_tools`, `super::handle_runtime_response`, `crate::tools::registry::ToolHandler`
- **被引用方**: `code_mode/mod.rs` 导出 `CodeModeExecuteHandler` 供 `spec.rs` 注册使用

## 实现备注
- 处理器只接受 `ToolPayload::Custom` 类型的调用，且工具名称必须匹配 `PUBLIC_TOOL_NAME`
- 使用 `codex_code_mode::parse_exec_source` 解析 JavaScript 源码中的 pragma 指令（如 `yield_time_ms`、`max_output_tokens`）
- 执行前会构建嵌套工具列表，允许 Code Mode 脚本内部调用其他工具
