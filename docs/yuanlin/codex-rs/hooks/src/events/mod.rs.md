# `mod.rs` -- 事件模块聚合入口

本文件是 `codex-rs/hooks/src/events` 模块的根文件，负责将所有 Hook 事件子模块统一导出。包含 `common`（内部公用工具）、`post_tool_use`、`pre_tool_use`、`session_start`、`stop` 和 `user_prompt_submit` 六个子模块的声明。
