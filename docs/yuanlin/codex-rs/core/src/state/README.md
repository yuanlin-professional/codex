# state/ -- 会话状态

## 功能概述

`state/` 管理 Codex 会话的运行时状态，分为三个层次：会话级服务（`SessionServices`）、会话级可变状态（`SessionState`）和轮次级状态（`ActiveTurn` / `TurnState`）。该模块是 Session 对象内部状态的拆分组织，将服务依赖和可变状态清晰分离。

## 目录结构

```
state/
├── mod.rs         # 模块入口，导出 SessionServices、SessionState、ActiveTurn、RunningTask、TaskKind
├── service.rs     # SessionServices：会话级服务容器（MCP 连接、工具审批、模型管理器等 30+ 服务）
├── session.rs     # SessionState：会话级可变状态（历史记录、速率限制、依赖环境、权限等）
├── turn.rs        # ActiveTurn / TurnState / RunningTask：轮次级状态和活动任务管理
└── session_tests.rs  # 会话状态测试
```

## 依赖关系

- **上游依赖**：`tokio`（异步锁、通道）、`indexmap`（有序映射）、`codex-protocol`（`ResponseInputItem`、`ReviewDecision`、`TokenUsage`）、`codex-otel`（SessionTelemetry）
- **内部依赖**：`context_manager/`（ContextManager）、`mcp/`（McpManager）、`models_manager/`（ModelsManager）、`plugins/`（PluginsManager）、`tools/`（ApprovalStore、CodeModeService、NetworkApprovalService）、`unified_exec/`（UnifiedExecProcessManager）、`agent/`（AgentControl）、`auth/`（AuthManager）
- **被依赖方**：`codex/`（Session 持有 SessionServices 和 SessionState）、`tasks/`（SessionTask 通过 SessionTaskContext 访问服务）

## 核心接口/API

| 接口/类型 | 说明 |
|---|---|
| `SessionServices` | 会话级服务容器，持有 30+ 个 Arc 包装的共享服务（MCP、模型、插件、审批等） |
| `SessionState` | 会话级可变状态：对话历史、速率限制、依赖环境、已授权权限等 |
| `ActiveTurn` | 当前活动轮次，持有 `RunningTask` 列表和共享 `TurnState` |
| `RunningTask` | 运行中任务：CancellationToken、AbortOnDropHandle、TurnContext、计时器 |
| `TaskKind` | 任务类型枚举：`Regular`、`Review`、`Compact` |
| `TurnState` | 轮次状态：挂起审批、挂起权限请求、挂起用户输入、token 使用、工具调用计数 |
