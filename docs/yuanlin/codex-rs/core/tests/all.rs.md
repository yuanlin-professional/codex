# `all.rs` — Core 集成测试入口聚合模块

`all.rs` 是 `codex-core` crate 集成测试的入口文件，将所有测试子模块聚合到单一测试二进制中。当前仅声明了 `mod suite;`，子模块位于 `tests/all/` 目录下（注释中描述，实际对应 `tests/suite/` 路径）。
