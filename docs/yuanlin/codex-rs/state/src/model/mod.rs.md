# `model/mod.rs` — 状态数据模型模块入口
该文件声明并重导出 `codex-state` crate 中所有数据模型子模块的公共类型：`AgentJob` 系列、`BackfillState`、`DirectionalThreadSpawnEdgeStatus`、`LogEntry/LogRow/LogQuery`、`Stage1Output` 系列记忆模型、`ThreadMetadata` 及其 Builder 等。同时导出内部使用的 `ThreadRow`、`AgentJobRow` 等行类型和工具函数。
