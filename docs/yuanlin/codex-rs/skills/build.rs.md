# `build.rs` — skills 构建脚本

`skills` crate 的 Cargo 构建脚本。扫描 `src/assets/samples` 目录并注册 `cargo:rerun-if-changed` 指令，确保在嵌入式系统技能文件发生变更时重新构建。
