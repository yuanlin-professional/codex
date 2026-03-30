# `main_execve_wrapper.rs` — execve 包装器二进制入口
仅在 Unix 平台可用，委托给 `codex_shell_escalation::main_execve_wrapper`。在非 Unix 平台输出错误信息并退出。
