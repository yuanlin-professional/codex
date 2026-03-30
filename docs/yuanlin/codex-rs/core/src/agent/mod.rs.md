# `mod.rs` — Agent 模块入口

该文件是 `agent` 模块的入口，声明并重新导出子模块。子模块包括：`agent_resolver`（目标解析）、`control`（控制平面）、`registry`（注册表）、`role`（角色配置）、`status`（状态映射）。主要导出 `AgentStatus`、`AgentControl`、`exceeds_thread_spawn_depth_limit`、`next_thread_spawn_depth`、`agent_status_from_event` 等核心类型和函数。
