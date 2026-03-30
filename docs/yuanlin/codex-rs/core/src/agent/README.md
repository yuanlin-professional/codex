# agent/ -- 代理系统

## 功能概述

`agent/` 实现了 Codex 的多代理（multi-agent）管理系统。该模块负责代理的注册、生命周期控制、角色配置解析以及代理名称解析。在 Codex 中，用户会话可以生成多个子代理（sub-agent），每个子代理运行在独立的线程中，通过 `AgentControl` 进行协调。角色系统（role）允许为不同类型的子代理加载不同的配置层。

## 目录结构

```
agent/
├── mod.rs              # 模块入口，导出核心类型（AgentControl、AgentStatus 等）
├── control.rs          # AgentControl：代理生命周期控制（生成、等待、消息发送、关闭）
├── registry.rs         # AgentRegistry：代理注册表，管理活跃代理和昵称分配
├── role.rs             # 角色配置系统，将命名角色层叠加到会话配置上
├── status.rs           # 代理状态推导，从事件推断 AgentStatus
├── agent_resolver.rs   # 代理目标解析器，将工具中的代理引用解析为 ThreadId
├── agent_names.txt     # 内置代理昵称列表
├── builtins/           # 内置角色定义
├── control_tests.rs    # 控制器测试
├── registry_tests.rs   # 注册表测试
└── role_tests.rs       # 角色测试
```

## 依赖关系

- **上游依赖**：`codex-protocol`（`AgentStatus`、`ThreadId`、`AgentPath`、`SessionSource`）、`codex-state`（线程生成边状态）
- **内部依赖**：`config/`（Config、AgentRoleConfig）、`config_loader/`（配置层栈）、`codex/`（Session）、`tools/handlers/multi_agents`（多代理工具处理器）
- **被依赖方**：`tools/handlers/`（多代理工具使用 AgentControl 生成/等待/关闭代理）、`state/`（SessionServices 持有 AgentControl）

## 核心接口/API

| 接口/类型 | 说明 |
|---|---|
| `AgentControl` | 代理生命周期控制器，提供 spawn/wait/send/close/list 等操作 |
| `AgentRegistry` | 代理注册表，管理活跃代理树、昵称分配和总数限制 |
| `AgentMetadata` | 代理元数据：agent_id、agent_path、昵称、角色、最后任务消息 |
| `apply_role_to_config()` | 将命名角色配置层叠加到当前 Config 上 |
| `resolve_agent_target()` | 将工具中的代理引用（名称或 ID）解析为 ThreadId |
| `agent_status_from_event()` | 从协议事件推导代理状态变更 |
| `exceeds_thread_spawn_depth_limit()` | 检查代理生成深度是否超限 |
