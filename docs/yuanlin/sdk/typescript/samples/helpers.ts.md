# `helpers.ts` -- 示例辅助函数

`helpers.ts` 提供了示例代码的辅助函数 `codexPathOverride()`，用于获取 Codex 可执行文件的路径。优先使用环境变量 `CODEX_EXECUTABLE`，回退到本地 Rust debug 构建路径 `../../codex-rs/target/debug/codex`。所有示例程序通过此函数定位 Codex 二进制文件。
