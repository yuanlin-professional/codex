# `command_runner.rs` — 命令运行器二进制入口
该文件是 `codex-command-runner` 可执行文件的入口点。它代理到 `elevated/command_runner_win.rs` 中的 Windows 实现，在非 Windows 平台上直接 panic。
