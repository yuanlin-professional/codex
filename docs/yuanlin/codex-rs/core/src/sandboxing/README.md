# sandboxing/ -- 沙箱适配层

## 功能概述

`sandboxing/` 是 core crate 内部的沙箱适配模块。沙箱策略选择和命令转换的核心逻辑位于外部 `codex-sandboxing` crate 中，本模块主要负责：定义 exec 专用的元数据类型（`ExecRequest`），将 `codex-sandboxing` 转换后的沙箱命令翻译回可执行的 `ExecRequest`，以及提供统一的沙箱执行入口。它是工具执行管道和沙箱策略之间的桥梁。

## 目录结构

```
sandboxing/
└── mod.rs    # ExecRequest 定义、从 SandboxExecRequest 的转换、执行入口函数
```

## 依赖关系

- **上游依赖**：`codex-sandboxing`（`SandboxExecRequest`、`SandboxType`）、`codex-protocol`（`SandboxPolicy`、`FileSystemSandboxPolicy`、`NetworkSandboxPolicy`、`WindowsSandboxLevel`）、`codex-network-proxy`（`NetworkProxy`）
- **内部依赖**：`exec`（底层执行函数 `execute_exec_request`、`ExecToolCallOutput`、`StdoutStream`）、`spawn`（沙箱环境变量常量）
- **被依赖方**：`tools/sandboxing.rs`（使用 ExecOptions 构建 ExecRequest）、`tools/runtimes/`（运行时调用 `execute_env`）、`unified_exec/`（进程管理器创建 ExecRequest）

## 核心接口/API

| 接口/类型 | 说明 |
|---|---|
| `ExecRequest` | 执行请求结构体：command、cwd、env、network、sandbox 类型、策略等 |
| `ExecOptions` | 执行选项：expiration（超时）和 capture_policy（输出捕获策略） |
| `ExecRequest::from_sandbox_exec_request()` | 从 `SandboxExecRequest` + `ExecOptions` 构建 `ExecRequest` |
| `execute_env()` | 执行沙箱命令并返回 `ExecToolCallOutput` |
| `execute_exec_request_with_after_spawn()` | 带 after_spawn 回调的执行入口 |
| `SandboxPermissions` | 沙箱权限（重导出自 codex-protocol） |
