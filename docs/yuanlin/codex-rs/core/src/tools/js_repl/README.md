# tools/js_repl/ -- JavaScript REPL

## 功能概述

`js_repl/` 实现了 Codex 内嵌的 JavaScript REPL（Read-Eval-Print Loop）内核。它通过 Node.js 子进程运行一个自定义的 JavaScript 内核，使用 meriyah 解析器进行代码分析，支持在沙箱环境中执行 JavaScript 代码。REPL 会话在轮次间保持状态，支持中断和重置。

## 目录结构

```
js_repl/
├── mod.rs                 # REPL 管理器：内核启动、命令执行、会话管理
├── kernel.js              # JavaScript 内核脚本（Node.js 运行时）
├── meriyah.umd.min.js     # meriyah 解析器（UMD 格式，嵌入）
└── mod_tests.rs           # 测试
```

## 依赖关系

- **上游依赖**：`tokio`（子进程管理、异步 I/O）、Node.js（运行时环境）
- **内部依赖**：`sandboxing/`（ExecOptions、SandboxCommand）、`tools/`（ToolRouter、SharedTurnDiffTracker）、`exec`（ExecCapturePolicy、ExecExpiration）
- **被依赖方**：`tools/handlers/js_repl.rs`（JsReplHandler 使用 REPL 管理器）

## 核心接口/API

| 接口/类型 | 说明 |
|---|---|
| JS REPL 管理器 | 管理 Node.js 内核进程的启动、执行和中断 |
| `kernel.js` | 内嵌的 JavaScript 执行内核 |
| `meriyah.umd.min.js` | 内嵌的 JavaScript 解析器（用于代码分析） |
| `JS_REPL_PRAGMA_PREFIX` | REPL 编译指示前缀 `// codex-js-repl:` |
