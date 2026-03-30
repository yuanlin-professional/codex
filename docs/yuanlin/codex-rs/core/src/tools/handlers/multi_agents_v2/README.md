# tools/handlers/multi_agents_v2/ -- 多代理工具 v2

## 功能概述

`multi_agents_v2/` 实现了 Codex 多代理工具的第二版接口，提供更丰富的代理交互模型。新增了 `assign_task`（任务分配）、`send_message`（消息发送）、`list_agents`（代理列表查询）等工具，并改进了 spawn 和 wait 的语义。

## 目录结构

```
multi_agents_v2/
├── spawn.rs          # spawn_agent v2：生成新子代理（增强版）
├── wait.rs           # wait_agent v2：等待子代理完成
├── assign_task.rs    # assign_task 工具：向代理分配任务
├── send_message.rs   # send_message 工具：向代理发送消息
├── message_tool.rs   # 消息工具辅助
├── list_agents.rs    # list_agents 工具：查询活跃代理列表
└── close_agent.rs    # close_agent v2：关闭子代理
```

## 依赖关系

- **上游依赖**：`codex-protocol`（ThreadId、AgentStatus）
- **内部依赖**：`agent/`（AgentControl、agent_resolver）、`tools/handlers/multi_agents_common.rs`（公共类型/常量）
- **被依赖方**：`tools/handlers/mod.rs`（通过 multi_agents_v2 模块导出）

## 核心接口/API

| 接口/类型 | 说明 |
|---|---|
| `spawn` | 生成新子代理 v2 |
| `wait` | 等待子代理完成 v2 |
| `assign_task` | 向代理分配特定任务 |
| `send_message` | 向代理发送消息 |
| `list_agents` | 查询活跃代理列表及状态 |
| `close_agent` | 关闭子代理 v2 |
