# `status.rs` — Agent 状态映射工具

该模块提供了从事件消息（`EventMsg`）派生 agent 状态（`AgentStatus`）的映射函数，以及判断状态是否为终态的辅助函数。`agent_status_from_event` 将 `TurnStarted` 映射为 `Running`、`TurnComplete` 映射为 `Completed`、`TurnAborted(Interrupted)` 映射为 `Interrupted`、`Error` 映射为 `Errored`、`ShutdownComplete` 映射为 `Shutdown`。`is_final` 判断状态不为 `PendingInit`、`Running` 或 `Interrupted` 时即为终态。
