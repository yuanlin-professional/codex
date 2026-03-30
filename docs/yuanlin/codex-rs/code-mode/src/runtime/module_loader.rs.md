# `module_loader.rs` — V8 模块加载与执行管理

## 功能概述
该文件负责在 V8 引擎中编译、实例化和执行 ES 模块。它提供了主模块求值、工具调用响应解析、Promise 完成状态检查以及动态 import 回调等核心功能。所有模块导入请求都会被拒绝并抛出异常，因为 code-mode 运行时不支持外部模块导入。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `evaluate_main_module` | `(scope, source_text) -> Result<Option<Global<Promise>>, String>` | 编译并执行主模块源代码，返回可能的 Promise |
| `resolve_tool_response` | `(scope, id, response) -> Result<(), String>` | 将工具调用的结果或错误解析回对应的 Promise resolver |
| `completion_state` | `(scope, pending_promise) -> CompletionState` | 检查模块执行的完成状态（Pending/Completed） |
| `dynamic_import_callback` | `(scope, ...) -> Option<Local<Promise>>` | 处理动态 import() 调用的回调 |
| `resolve_module` | `(scope, specifier) -> Option<Local<Module>>` | 模块解析回调，始终拒绝并抛出异常 |

## 依赖关系
- **引用的 crate**: `serde_json`, `v8`
- **被引用方**: code-mode 运行时核心模块

## 实现备注
- 所有模块导入（包括静态和动态）都会被拒绝，code-mode 不支持外部依赖
- 使用 `EXIT_SENTINEL` 字符串异常来实现 `exit()` 功能，通过 `is_exit_exception` 检测
- Promise 状态检查处理了 Pending、Fulfilled 和 Rejected 三种状态
