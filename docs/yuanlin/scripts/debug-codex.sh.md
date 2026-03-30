# `debug-codex.sh` -- 使用 cargo 运行最新的 codex-rs 调试构建

该脚本用于 VSCode 扩展调试场景：将 VSCode 设置中的 `chatgpt.cliExecutable` 指向此脚本后，每次启动扩展时都会自动通过 `cargo run` 编译并运行 `codex-rs` 仓库中最新的 `codex` 二进制文件，无需手动构建。脚本自动定位到 `codex-rs` 目录并将所有参数透传给 `codex` 命令。
