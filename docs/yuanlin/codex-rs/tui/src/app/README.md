# src/app/

## 功能概述

`app/` 子目录包含 `App` 主结构体的辅助模块，负责将应用主控逻辑拆分为关注点清晰的子模块。这些模块处理多 Agent 导航、App Server 事件适配、服务端请求管理、线程加载以及交互式重放状态。

核心设计原则：将纯逻辑（如导航遍历、请求追踪）从 `App` 中分离出来，使其可独立测试。

## 目录结构

```
app/
├── agent_navigation.rs          # 多 Agent 选择器导航和标签状态
├── app_server_adapter.rs        # TUI 与 App Server 之间的临时适配层
├── app_server_requests.rs       # 待处理 App Server 请求追踪
├── loaded_threads.rs            # 子 Agent 线程发现（spawn tree 遍历）
└── pending_interactive_replay.rs # 交互式提示重放状态机
```

## 核心接口/API

| 文件 | 核心类型/函数 | 说明 |
|------|--------------|------|
| `agent_navigation.rs` | `AgentNavigationState` | 多 Agent 选择器排序和键盘循环遍历状态容器；维护 first-seen spawn 顺序 |
| `agent_navigation.rs` | `AgentNavigationDirection` | 遍历方向枚举（Previous/Next） |
| `app_server_adapter.rs` | `App::handle_app_server_event()` | 处理来自 App Server 的事件流，将协议事件映射为 TUI 内部状态变更 |
| `app_server_requests.rs` | `PendingAppServerRequests` | 追踪待处理的执行审批、文件变更审批、权限审批、用户输入和 MCP 请求 |
| `app_server_requests.rs` | `AppServerRequestResolution` | 请求解析结果，包含请求 ID 和 JSON 响应值 |
| `loaded_threads.rs` | `find_loaded_subagent_threads_for_primary()` | 纯同步函数：从扁平线程列表中通过 spawn tree 遍历找出主线程的所有后代子 Agent |
| `loaded_threads.rs` | `LoadedSubagentThread` | 发现的子 Agent 线程元数据 |
| `pending_interactive_replay.rs` | `PendingInteractiveReplayState` | 追踪哪些交互式提示（审批、用户输入、MCP 请求）仍然未解决，用于线程切换时的快照重放过滤 |
