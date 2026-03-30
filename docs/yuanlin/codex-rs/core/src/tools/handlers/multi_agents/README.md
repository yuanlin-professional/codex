# tools/handlers/multi_agents/ -- 多代理工具 v1

## 功能概述

`multi_agents/` 实现了 Codex 多代理工具的第一版接口，提供子代理的生成（spawn）、等待（wait）、消息发送（send_input）、恢复（resume）和关闭（close）操作。这些工具允许 AI 代理创建和管理子代理，实现并行任务执行和层级化协作。

## 目录结构

```
multi_agents/
├── spawn.rs          # spawn_agent 工具：生成新子代理
├── wait.rs           # wait_agent 工具：等待子代理完成
├── send_input.rs     # send_input 工具：向子代理发送输入
├── resume_agent.rs   # resume_agent 工具：恢复已暂停的子代理
└── close_agent.rs    # close_agent 工具：关闭子代理
```

## 依赖关系

- **上游依赖**：`codex-protocol`（ThreadId、AgentStatus、SubAgentSource）
- **内部依赖**：`agent/`（AgentControl、agent_resolver）、`tools/handlers/multi_agents_common.rs`（公共类型/常量）
- **被依赖方**：`tools/handlers/mod.rs`（通过 multi_agents 模块导出）

## 核心接口/API

| 接口/类型 | 说明 |
|---|---|
| `spawn` | 生成新子代理，支持指定角色和分叉模式 |
| `wait` | 等待子代理完成，支持超时控制 |
| `send_input` | 向子代理发送用户输入 |
| `resume_agent` | 恢复已暂停的子代理 |
| `close_agent` | 关闭子代理 |
