# `suite/mod.rs` — TUI 测试套件模块聚合

`suite/mod.rs` 将原先独立的集成测试聚合为子模块。它声明了以下测试子模块：`model_availability_nux`、`no_panic_on_startup`、`status_indicator`、`vt100_history`、`vt100_live_commit`。每个子模块对应一个特定的 TUI 功能测试场景。
