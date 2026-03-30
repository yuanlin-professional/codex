# tools/runtimes/ -- 工具运行时

## 功能概述

`runtimes/` 包含具体工具的 `ToolRuntime` trait 实现。每个运行时封装特定工具的沙箱执行逻辑，通过 `ToolOrchestrator` 进行审批和沙箱选择后执行实际操作。运行时设计保持小而专注，复用编排器实现审批 + 沙箱 + 重试的统一流程。

## 目录结构

```
runtimes/
├── mod.rs                     # 模块入口，定义 build_sandbox_command() 辅助函数
├── apply_patch.rs             # 补丁应用运行时
├── shell.rs                   # Shell 命令运行时
├── shell/                     # Shell 运行时子模块
│   ├── unix_escalation.rs     # Unix 特权提升（通过 EscalateServer）
│   ├── unix_escalation_tests.rs
│   └── zsh_fork_backend.rs    # Zsh fork 后端
├── unified_exec.rs            # 统一执行运行时
├── apply_patch_tests.rs       # 补丁应用运行时测试
└── mod_tests.rs               # 模块测试
```

## 依赖关系

- **上游依赖**：`codex-sandboxing`（SandboxCommand、SandboxManager）、`codex-shell-escalation`（EscalateServer）、`codex-execpolicy`（执行策略评估）
- **内部依赖**：`sandboxing/`（ExecRequest）、`tools/sandboxing`（ToolRuntime trait、SandboxAttempt、ToolCtx）、`tools/orchestrator`（ToolOrchestrator）
- **被依赖方**：`tools/handlers/`（处理器调用运行时执行工具）

## 核心接口/API

| 接口/类型 | 说明 |
|---|---|
| `build_sandbox_command()` | 从分词命令行构建 SandboxCommand |
| Shell 运行时 | 实现 ToolRuntime，处理 Shell 命令的沙箱转换和执行 |
| Apply Patch 运行时 | 实现 ToolRuntime，处理补丁应用的沙箱执行 |
| Unified Exec 运行时 | 实现 ToolRuntime，处理统一执行进程的沙箱启动 |
