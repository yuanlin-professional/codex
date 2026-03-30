# `main.rs` — TUI 应用程序入口

TUI 二进制的 `main` 函数。解析顶层 CLI 参数（通过 `clap`），合并配置覆盖项，
调用 `run_main` 启动 TUI 会话，退出后打印 token 使用量摘要。
通过 `arg0_dispatch_or_else` 支持 busybox 风格的多命令分发。
