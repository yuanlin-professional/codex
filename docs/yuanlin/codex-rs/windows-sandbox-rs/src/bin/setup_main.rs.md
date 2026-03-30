# `setup_main.rs` — 沙盒安装程序二进制入口
该文件是 `codex-windows-sandbox-setup` 可执行文件的入口点。它代理到 `setup_main_win.rs` 中的 Windows 实现，在非 Windows 平台上直接 panic。
