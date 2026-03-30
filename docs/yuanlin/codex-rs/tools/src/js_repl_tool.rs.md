# `js_repl_tool.rs` -- JavaScript REPL 工具定义

## 功能概述
定义 `js_repl`（持久化 Node.js 内核的 JavaScript REPL）和 `js_repl_reset`（重启 REPL 内核并清除绑定）两个工具。`js_repl` 使用 Freeform 格式而非 JSON，通过 Lark 语法阻止常见的格式错误（JSON 包装、引号字符串、Markdown 代码块），支持首行 pragma（如 `// codex-js-repl: timeout_ms=15000`）。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `create_js_repl_tool` | `fn() -> ToolSpec` | 创建 js_repl Freeform 工具 |
| `create_js_repl_reset_tool` | `fn() -> ToolSpec` | 创建 js_repl_reset Function 工具 |

## 依赖关系
- **被引用方**: `lib.rs` 公开导出

## 实现备注
- Lark 语法定义防止 LLM 发送 JSON 或 Markdown 格式的代码
