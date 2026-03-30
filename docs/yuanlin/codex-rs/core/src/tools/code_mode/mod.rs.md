# `mod.rs` — Code Mode 模块核心

## 功能概述
Code Mode 模块的主入口文件，提供 JavaScript 代码执行的完整生命周期管理。包含 `CodeModeService` 服务封装、执行上下文管理、嵌套工具调用支持、运行时响应处理以及输出截断策略。该模块是 Codex 中"代码模式"功能的核心实现，允许模型通过 JavaScript 运行时执行代码并调用其他工具。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ExecContext` | struct | 执行上下文，持有 `Session` 和 `TurnContext` 的 Arc 引用 |
| `CodeModeService` | struct | Code Mode 服务封装，管理底层 `codex_code_mode::CodeModeService` |
| `CoreTurnHost` | struct | 实现 `CodeModeTurnHost` trait，桥接 Code Mode 运行时与核心工具系统 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `CodeModeService::start_turn_worker` | `async fn start_turn_worker(...)` | 启动 Code Mode 的 turn worker，仅在 CodeMode feature 开启时生效 |
| `handle_runtime_response` | `async fn handle_runtime_response(exec, response, max_output_tokens, started_at)` | 处理三种运行时响应：Yielded/Terminated/Result，统一格式化并截断输出 |
| `build_enabled_tools` | `async fn build_enabled_tools(exec)` | 构建 Code Mode 脚本可调用的工具定义列表 |
| `call_nested_tool` | `async fn call_nested_tool(exec, tool_runtime, tool_name, input, cancellation_token)` | 执行嵌套工具调用，支持 MCP、Function、Freeform 三种载荷类型 |
| `build_nested_router` | `async fn build_nested_router(exec)` | 构建嵌套工具路由器，排除 Code Mode 自身工具 |
| `truncate_code_mode_result` | `fn truncate_code_mode_result(items, max_output_tokens)` | 根据 token 策略截断执行结果 |

## 依赖关系
- **引用的 crate**: `codex_code_mode`, `codex_protocol`, `codex_features`, `codex_tools`, `codex_utils_output_truncation`, `tokio_util`, `uuid`
- **子模块**: `execute_handler`, `response_adapter`, `wait_handler`
- **被引用方**: `tools/spec.rs` 注册处理器, `tools/handlers/` 使用 Code Mode 服务

## 实现备注
- Code Mode 脚本禁止递归调用自身（`PUBLIC_TOOL_NAME`）
- `CoreTurnHost::notify` 会通过 `inject_response_items` 将中间输出注入到对话流中
- 嵌套工具调用支持三种载荷模式：MCP 工具、Function 工具（JSON 参数）、Freeform 工具（字符串输入）
- 输出截断使用 `resolve_max_tokens` 确定 token 上限，优先使用 token 级别截断，纯文本时使用格式化截断
