# `all.rs` — TUI 集成测试入口聚合模块

`all.rs` 是 TUI crate 的集成测试入口文件，将所有独立的测试模块聚合到一个二进制中。它通过条件编译 (`#[cfg(feature = "vt100-tests")]`) 有选择地引入 `test_backend` 模块，并始终引入 `suite` 子模块。同时保留了对 `codex_cli` 的 `use` 引用以满足 `cargo-shear` 的 dev-dep 检查需求。
