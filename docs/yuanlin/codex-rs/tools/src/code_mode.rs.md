# `code_mode.rs` -- 代码模式工具增强

## 功能概述
为 Codex 的代码模式（code mode）提供工具定义增强功能。`augment_tool_spec_for_code_mode` 向工具描述注入代码模式执行示例，`tool_spec_to_code_mode_tool_definition` 将 ToolSpec 转换为 codex_code_mode 的工具定义格式，`create_code_mode_tool` 创建代码模式控制工具，`create_wait_tool` 创建用于代码模式的 wait 工具。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `augment_tool_spec_for_code_mode` | `fn(spec) -> ToolSpec` | 向工具描述注入代码模式示例 |
| `tool_spec_to_code_mode_tool_definition` | `fn(spec) -> Option<CodeModeToolDefinition>` | ToolSpec 转 code mode 定义 |
| `create_code_mode_tool` | `fn() -> ToolSpec` | 创建 code_mode 控制工具 |
| `create_wait_tool` | `fn() -> ToolSpec` | 创建 wait 工具 |

## 依赖关系
- **引用的 crate**: `codex_code_mode`
- **被引用方**: `lib.rs` 公开导出
