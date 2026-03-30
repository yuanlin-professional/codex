# `control.rs` — 多 Agent 控制平面核心实现

## 功能概述

`AgentControl` 是多 agent 操作的控制平面句柄，由每个 session（通过 `SessionServices`）持有。它负责管理 agent 的完整生命周期，包括创建（spawn）、恢复（resume）、消息发送、状态查询、中断、关闭等操作。一个 `AgentControl` 实例在同一根线程/session 树中至多创建一次，并在该树下所有子 agent 间共享，使得注册表（registry）的作用域限定在该根线程而非整个 `ThreadManager`。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `AgentControl` | struct | 多 agent 控制平面句柄，包含对 `ThreadManagerState` 的 `Weak` 引用和共享的 `AgentRegistry` |
| `LiveAgent` | struct | 刚创建/恢复的活跃 agent 信息，含 `thread_id`、`metadata`、`status` |
| `ListedAgent` | struct | 用于列表展示的 agent 信息，含名称、状态、最后任务消息 |
| `SpawnAgentOptions` | struct | spawn 选项，支持 `fork_parent_spawn_call_id` 用于从父线程历史分叉 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn(manager: Weak<ThreadManagerState>) -> Self` | 创建新的 `AgentControl`，绑定到全局线程管理器 |
| `spawn_agent` | `async fn(&self, config, op, source) -> Result<ThreadId>` | 创建新 agent 线程并提交初始提示词 |
| `spawn_agent_with_metadata` | `async fn(&self, config, op, source, options) -> Result<LiveAgent>` | 创建 agent 并返回完整元数据，支持 fork 选项 |
| `resume_agent_from_rollout` | `async fn(&self, config, thread_id, source) -> Result<ThreadId>` | 从已有 rollout 文件恢复 agent 线程（含递归恢复后代） |
| `send_input` | `async fn(&self, agent_id, op) -> Result<String>` | 向已有 agent 线程发送用户输入 |
| `send_inter_agent_communication` | `async fn(&self, agent_id, communication) -> Result<String>` | 发送 agent 间通信消息 |
| `interrupt_agent` | `async fn(&self, agent_id) -> Result<String>` | 中断指定 agent 的当前任务 |
| `shutdown_live_agent` | `async fn(&self, agent_id) -> Result<String>` | 优雅关闭指定 agent |
| `close_agent` | `async fn(&self, agent_id) -> Result<String>` | 标记 agent 为已关闭并关闭其后代树 |
| `get_status` | `async fn(&self, agent_id) -> AgentStatus` | 获取 agent 的当前状态 |
| `subscribe_status` | `async fn(&self, agent_id) -> Result<watch::Receiver<AgentStatus>>` | 订阅 agent 状态变更 |
| `list_agents` | `async fn(&self, source, prefix) -> Result<Vec<ListedAgent>>` | 列出匹配路径前缀的所有活跃 agent |
| `resolve_agent_reference` | `async fn(&self, ..., reference) -> Result<ThreadId>` | 将 agent 路径引用解析为 ThreadId |
| `format_environment_context_subagents` | `async fn(&self, parent_id) -> String` | 格式化子 agent 上下文信息用于环境提示 |
| `render_input_preview` | `fn(op: &Op) -> String` | 渲染输入操作的文本预览 |

## 依赖关系

- **引用的 crate**: `codex_protocol`、`codex_features`、`codex_state`
- **引用的内部模块**: `agent::registry`、`agent::role`、`agent::status`、`config`、`thread_manager`、`rollout`、`session_prefix`、`shell_snapshot`、`state_db`
- **被引用方**: `SessionServices`（持有 `AgentControl`）、多 agent 工具函数、TUI 层

## 实现备注

- **Weak 引用设计**: `AgentControl` 通过 `Weak<ThreadManagerState>` 避免引用循环（`ThreadManagerState -> CodexThread -> Session -> SessionServices -> ThreadManagerState`）。
- **Fork 机制**: 支持从父线程的 rollout 历史分叉创建子 agent，fork 前会先刷新（flush）父线程的 rollout 数据。
- **完成通知**: 通过 `maybe_start_completion_watcher` 启动异步任务监听子 agent 完成，支持 v1（注入用户消息）和 v2（agent 间通信）两种通知方式。
- **树形关闭**: `shutdown_agent_tree` 通过 DFS 遍历活跃后代并逐一关闭，确保无孤儿线程。
- **昵称分配**: agent spawn 时从配置的候选列表中随机分配昵称，支持角色特定的候选列表。
- **Agent 名称列表**: 从 `agent_names.txt` 文件加载默认昵称候选池。
- **Shell 快照继承**: 子 agent 可继承父 agent 的 shell 环境快照和执行策略。
