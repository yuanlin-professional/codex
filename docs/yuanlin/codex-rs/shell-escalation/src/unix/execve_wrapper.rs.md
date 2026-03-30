# `execve_wrapper.rs` — execve 包装器 CLI 入口
定义 `ExecveWrapperCli` 结构体和 `main_execve_wrapper` 异步入口函数。使用 clap 解析 file 和 argv 参数，初始化 tracing 日志后调用 `run_shell_escalation_execve_wrapper`。
