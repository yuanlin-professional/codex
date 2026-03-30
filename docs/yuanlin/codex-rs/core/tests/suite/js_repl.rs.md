# `js_repl.rs` — 测试模块

## 测试覆盖范围
JavaScript REPL（js_repl）工具功能，包括 Node 兼容性检查、绑定持久化、失败单元处理、内置工具调用、安全限制等。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `js_repl_is_not_advertised_when_startup_node_is_incompatible` | Node 版本不兼容时，js_repl 工具不出现在工具列表中 |
| `js_repl_persists_top_level_destructured_bindings_and_supports_tla` | 顶层解构绑定跨 REPL 单元持久化，支持顶层 await |
| `js_repl_failed_cells_commit_initialized_bindings_only` | 失败单元只提交已初始化的绑定，未初始化的绑定不可访问 |
| `js_repl_failed_cells_preserve_initialized_lexical_destructuring_bindings` | 失败单元保留已初始化的词法解构绑定 |
| `js_repl_link_failures_keep_prior_module_state` | 模块链接失败不影响之前的模块状态 |
| `js_repl_failed_cells_do_not_commit_unreached_hoisted_bindings` | 失败单元不提交未到达的提升绑定 |
| `js_repl_failed_cells_do_not_preserve_hoisted_function_reads_before_declaration` | 失败单元不保留声明前的提升函数读取 |
| `js_repl_failed_cells_preserve_functions_when_declaration_sites_are_reached` | 声明位置已到达时，失败单元保留函数绑定 |
| `js_repl_failed_cells_preserve_prior_binding_writes_without_new_bindings` | 失败单元保留对已有绑定的写入 |
| `js_repl_failed_cells_var_persistence_boundaries` | var 声明在失败单元中的持久化边界（多种场景） |
| `js_repl_failed_cells_commit_non_empty_loop_vars_but_skip_empty_loops` | 失败单元提交非空循环变量但跳过空循环变量 |
| `js_repl_keeps_function_to_string_stable` | 函数的 toString() 输出保持稳定，不包含内部标记 |
| `js_repl_allows_globalthis_shadowing_with_instrumented_bindings` | 允许对 globalThis 的遮蔽与仪表化绑定共存 |
| `js_repl_can_invoke_builtin_tools` | js_repl 中可以调用内置工具（如 list_mcp_resources） |
| `js_repl_tool_call_rejects_recursive_js_repl_invocation` | js_repl 拒绝递归调用自身 |
| `js_repl_does_not_expose_process_global` | js_repl 中不暴露 process 全局对象 |
| `js_repl_exposes_codex_path_helpers` | js_repl 暴露 codex.cwd 和 codex.homeDir 路径辅助工具 |
| `js_repl_blocks_sensitive_builtin_imports` | js_repl 阻止导入敏感内置模块（如 node:process） |
