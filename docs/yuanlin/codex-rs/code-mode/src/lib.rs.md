# `lib.rs` -- Code Mode crate 入口

## 功能概述

这是 `codex-code-mode` crate 的入口文件。它声明了四个内部模块（`description`、`response`、`runtime`、`service`），并通过 `pub use` 统一导出所有公共 API。该 crate 提供了一个在隔离 V8 上下文中执行 JavaScript 代码的"Code Mode"功能，包括 exec/wait 工具定义、运行时管理和服务层接口。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| 常量 `PUBLIC_TOOL_NAME` | `"exec"` | exec 工具的公开名称 |
| 常量 `WAIT_TOOL_NAME` | `"wait"` | wait 工具的公开名称 |

## 依赖关系

- **引用的 crate**: 通过子模块间接依赖 `serde`, `serde_json`, `v8`, `tokio`
- **被引用方**: 上层 Codex 核心模块

## 实现备注

导出的 API 涵盖：exec 源码解析、工具描述构建、JSON Schema 到 TypeScript 转换、运行时请求/响应类型、以及 CodeModeService/CodeModeTurnHost/CodeModeTurnWorker 服务接口。
