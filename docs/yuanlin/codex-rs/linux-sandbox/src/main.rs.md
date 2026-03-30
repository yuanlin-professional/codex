# `main.rs` -- Linux 沙箱可执行文件入口

此文件是 `codex-linux-sandbox` 二进制程序的入口点。它仅调用 `codex_linux_sandbox::run_main()` 来启动沙箱辅助进程。注释中说明 cwd、环境变量和命令参数在最终的 `execv` 调用中会被保留，因此调用者有责任确保这些值正确。
