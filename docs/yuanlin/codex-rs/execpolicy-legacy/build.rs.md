# `build.rs` — 构建脚本：监听默认策略文件变更

当 `src/default.policy` 文件发生变更时，通知 Cargo 重新编译本 crate。这确保了内嵌的默认策略始终与源文件保持同步。
