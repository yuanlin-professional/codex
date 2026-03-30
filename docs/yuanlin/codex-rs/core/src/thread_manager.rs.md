# `thread_manager.rs` — 线程管理器：多会话线程的创建、恢复与 Fork

## 功能概述

`thread_manager.rs`（约 1039 行）实现了 `ThreadManager`，负责创建、管理和跟踪多个 Codex 会话线程（Thread）。它封装了线程的启动（新建/恢复/Fork）、关闭、模型列表查询、MCP 服务器刷新、技能监视器管理等功能。内部通过 `ThreadManagerState` 持有所有活跃线程的 HashMap，并支持通过 broadcast channel 通知线程创建事件。文件还实现了 Fork 快照逻辑（按用户消息边界截断、中断边界追加）以及批量线程关闭。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ThreadManager` | struct | 线程管理器，持有 `Arc<ThreadManagerState>` 和可选的测试 codex_home guard |
| `ThreadManagerState` | struct | 共享状态：threads HashMap、thread_created_tx、auth/models/environment/skills/plugins/mcp 管理器、skills_watcher、session_source、ops_log（测试用） |
| `NewThread` | struct | 新创建的线程，包含 thread_id、thread（Arc<CodexThread>）、session_configured 事件 |
| `ForkSnapshot` | enum | Fork 快照模式：TruncateBeforeNthUserMessage(usize) / Interrupted |
| `ThreadShutdownReport` | struct | 线程关闭报告：completed / submit_failed / timed_out 列表 |
| `SnapshotTurnState` | struct | 快照 Turn 状态：ends_mid_turn、active_turn_id、active_turn_start_index |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ThreadManager::new` | `fn new(config, auth_manager, session_source, ...)` | 创建线程管理器（含 ModelsManager、PluginsManager、McpManager、SkillsWatcher 初始化） |
| `ThreadManager::start_thread` | `async fn start_thread(config) -> CodexResult<NewThread>` | 启动新线程 |
| `ThreadManager::start_thread_with_tools` | `async fn ...` | 启动新线程（带动态工具） |
| `ThreadManager::resume_thread_from_rollout` | `async fn resume_thread_from_rollout(config, rollout_path, ...)` | 从 rollout 文件恢复线程 |
| `ThreadManager::fork_thread` | `async fn fork_thread(snapshot, config, path, ...)` | Fork 现有线程 |
| `ThreadManager::shutdown_all_threads_bounded` | `async fn ...(timeout) -> ThreadShutdownReport` | 在超时内并发关闭所有线程 |
| `ThreadManager::get_thread` | `async fn get_thread(thread_id) -> CodexResult<Arc<CodexThread>>` | 获取线程 |
| `ThreadManager::remove_thread` | `async fn remove_thread(thread_id)` | 移除线程 |
| `ThreadManager::refresh_mcp_servers` | `async fn ...` | 向所有线程发送 MCP 刷新请求 |
| `ThreadManagerState::spawn_thread` | `async fn spawn_thread(config, initial_history, ...)` | 核心线程 spawn 方法：调用 Codex::spawn 并注册线程 |
| `ThreadManagerState::spawn_thread_with_source` | `async fn ...` | 带 session_source 的 spawn |
| `truncate_before_nth_user_message` | `fn ...` | 按第 N 个用户消息截断 rollout |
| `snapshot_turn_state` | `fn ...` | 分析 rollout 的 Turn 边界状态 |
| `append_interrupted_boundary` | `fn ...` | 追加中断边界标记 |
| `build_skills_watcher` | `fn ...` | 构建技能监视器（含文件变更监听） |

## 依赖关系

- **引用的 crate**: `codex_protocol`（ThreadId、Op、InitialHistory 等）、`codex_exec_server`（EnvironmentManager）、`codex_app_server_protocol`（ThreadHistoryBuilder）
- **被引用方**: `app_server`（App Server 使用 ThreadManager 管理多线程）、`tui_app`（TUI 使用 ThreadManager）、集成测试

## 实现备注

1. **Box::pin 延迟**：所有 spawn 方法使用 `Box::pin` 避免将完整 spawn 路径内联到每个调用者的 async 状态机中。
2. **Fork 快照策略**：`TruncateBeforeNthUserMessage` 在第 N 个用户消息前截断；当 N 超出范围且源线程正在 mid-turn 时，截断到活跃 Turn 开始处。`Interrupted` 模式追加 `<turn_aborted>` 标记。
3. **SkillsWatcher 联动**：技能变更时自动清除 SkillsManager 缓存。在 current-thread 测试运行时使用 noop watcher 避免阻塞。
4. **测试模式**：通过 `FORCE_TEST_THREAD_MANAGER_BEHAVIOR` 全局标志启用测试行为（ops_log 捕获、noop skills watcher）。
5. **批量关闭**：使用 `FuturesUnordered` 并发执行所有线程关闭，已完成的从跟踪 map 中移除。
