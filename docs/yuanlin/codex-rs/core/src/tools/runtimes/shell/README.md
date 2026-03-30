# tools/runtimes/shell/ -- Shell 运行时

## 功能概述

`shell/` 是 Shell 命令运行时的平台特定子模块，包含 Unix 系统上的特权提升逻辑和 Zsh fork 后端。特权提升允许在需要时通过 `EscalateServer` 以提升权限执行命令，Zsh fork 后端提供高效的 Zsh 进程复用机制。

## 目录结构

```
shell/
├── unix_escalation.rs          # Unix 特权提升实现（通过 EscalateServer）
├── unix_escalation_tests.rs    # 特权提升测试
└── zsh_fork_backend.rs         # Zsh fork 后端：高效的 Zsh 进程复用
```

## 依赖关系

- **上游依赖**：`codex-shell-escalation`（EscalateServer）、`codex-shell-command`（Shell 命令解析）、`codex-execpolicy`（执行策略）
- **内部依赖**：`sandboxing/`（ExecRequest）、`tools/sandboxing`（ToolCtx、SandboxAttempt）
- **被依赖方**：`tools/runtimes/shell.rs`（Shell 运行时使用提升逻辑和 fork 后端）

## 核心接口/API

| 接口/类型 | 说明 |
|---|---|
| Unix 特权提升 | 通过 EscalateServer 在需要时以提升权限执行 Shell 命令 |
| Zsh fork 后端 | 使用 fork 机制高效复用 Zsh 进程，避免重复启动开销 |
