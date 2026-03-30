# `router.rs` — 工具路由器

## 功能概述
`ToolRouter` 是工具系统的路由层，负责从配置构建工具规格和注册表、将模型的工具调用请求路由到正确的处理器。支持多种调用类型（FunctionCall、ToolSearchCall、CustomToolCall、LocalShellCall）的解析，以及 js_repl_tools_only 模式下的调用过滤。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ToolCall` | struct | 工具调用：tool_name、tool_namespace、call_id、payload |
| `ToolRouter` | struct | 工具路由器：registry + specs + model_visible_specs |
| `ToolRouterParams<'a>` | struct | 路由器构建参数：mcp_tools、app_tools、discoverable_tools、dynamic_tools |
| `ToolCallSource` | enum | re-export from context: Direct/JsRepl/CodeMode |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `from_config` | `fn(config, params) -> Self` | 从 ToolsConfig 构建路由器，code_mode_only 时过滤 model_visible_specs |
| `build_tool_call` | `async fn(session, item) -> Result<Option<ToolCall>, FunctionCallError>` | 将 ResponseItem 解析为 ToolCall，识别 MCP 工具 |
| `dispatch_tool_call_with_code_mode_result` | `async fn(session, turn, tracker, call, source)` | 调度工具调用，js_repl_tools_only 模式下阻止直接调用 |
| `specs` / `model_visible_specs` | `fn() -> Vec<ToolSpec>` | 获取全部/模型可见的工具规格列表 |

## 依赖关系
- **引用的 crate**: `codex_tools`, `codex_protocol`, `rmcp`
- **内部依赖**: `crate::tools::registry`, `crate::tools::spec`, `crate::tools::discoverable`
- **被引用方**: `crate::codex` 主循环、`parallel.rs`、`code_mode/mod.rs`

## 实现备注
- `code_mode_only_enabled` 时，`model_visible_specs` 排除 Code Mode 嵌套工具
- `js_repl_tools_only` 模式下，仅 `js_repl` 和 `js_repl_reset` 允许直接调用
- `LocalShellCall` 的 call_id 优先使用 `call_id` 字段，回退到 `id` 字段
- MCP 工具识别通过 `session.parse_mcp_tool_name` 完成
