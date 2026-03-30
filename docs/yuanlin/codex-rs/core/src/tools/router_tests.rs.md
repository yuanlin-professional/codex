# `router_tests.rs` — 测试模块

## 测试覆盖范围
验证工具路由器的调度过滤和工具调用构建逻辑。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `js_repl_tools_only_blocks_direct_tool_calls` | js_repl_tools_only 模式下阻止非 js_repl 工具的直接调用 |
| `js_repl_tools_only_allows_js_repl_source_calls` | js_repl_tools_only 模式下允许来自 JsRepl 来源的工具调用 |
| `build_tool_call_uses_namespace_for_registry_name` | FunctionCall 的 namespace 正确传递到 ToolCall 中 |
