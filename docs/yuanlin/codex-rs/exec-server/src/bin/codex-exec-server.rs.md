# `codex-exec-server.rs` — exec-server 可执行入口

exec-server 的 CLI 入口文件。使用 `clap` 解析 `--listen` 参数（默认 `ws://127.0.0.1:0`），然后调用 `codex_exec_server::run_main_with_listen_url` 启动异步 WebSocket 服务器。
