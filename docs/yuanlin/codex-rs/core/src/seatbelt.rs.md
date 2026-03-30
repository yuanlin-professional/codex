# `seatbelt.rs` — macOS Seatbelt 沙箱命令启动

该文件仅在 `target_os = "macos"` 条件下编译，提供 `spawn_command_under_seatbelt` 异步函数，用于在 macOS 的 Seatbelt 沙箱环境下启动子进程。该函数接收命令、工作目录、沙箱策略、网络代理等参数，调用 `codex_sandboxing` crate 生成 Seatbelt 命令参数，设置 `CODEX_SANDBOX_ENV_VAR` 环境变量为 `"seatbelt"`，然后通过 `spawn_child_async` 异步派生子进程。依赖 `codex_sandboxing::seatbelt` 模块进行沙箱策略到 Seatbelt 配置文件参数的转换，依赖 `codex_network_proxy::NetworkProxy` 处理网络代理。
