# tools/code_mode/ -- 代码模式

## 功能概述

`code_mode/` 实现了 Codex 的代码模式（Code Mode）工具，提供 `execute` 和 `wait` 两个工具操作。代码模式允许模型通过 `CodeModeTurnHost` 管理代码执行的轮次，将嵌套的工具调用路由回主工具系统。它封装了 `codex-code-mode` crate 的功能，将其集成到 core 的工具管道中。

## 目录结构

```
code_mode/
├── mod.rs                       # CodeModeService、ExecContext、工具常量
├── execute_handler.rs           # CodeModeExecuteHandler：execute 工具处理器
├── wait_handler.rs              # CodeModeWaitHandler：wait 工具处理器
├── response_adapter.rs          # 响应适配器（将 CodeMode 响应转为 FunctionCallOutput）
└── execute_handler_tests.rs     # execute 处理器测试
```

## 依赖关系

- **上游依赖**：`codex-code-mode`（CodeModeService、CodeModeTurnHost、RuntimeResponse）
- **内部依赖**：`tools/`（ToolRouter、ToolCallRuntime）、`tools/parallel`（并行执行）、`codex/`（Session、TurnContext）
- **被依赖方**：`tools/handlers/mod.rs`（导出 CodeModeExecuteHandler 和 CodeModeWaitHandler）、`state/`（SessionServices 持有 CodeModeService）

## 核心接口/API

| 接口/类型 | 说明 |
|---|---|
| `CodeModeService` | 代码模式服务，封装 `codex_code_mode::CodeModeService` |
| `CodeModeExecuteHandler` | execute 工具处理器：执行代码模式轮次 |
| `CodeModeWaitHandler` | wait 工具处理器：等待代码模式轮次完成 |
| `ExecContext` | 执行上下文，包含 session 和 turn |
| `PUBLIC_TOOL_NAME` | 代码模式公开工具名 |
| `WAIT_TOOL_NAME` | 代码模式等待工具名 |
