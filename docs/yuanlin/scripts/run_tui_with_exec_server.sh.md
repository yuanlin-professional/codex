# `run_tui_with_exec_server.sh` -- 启动 exec-server 并连接 TUI 客户端

## 功能概述

该脚本自动化了 Codex TUI 本地开发的启动流程：先在后台通过 `cargo run` 启动 `codex-exec-server`（WebSocket 执行服务器），等待其绑定端口并输出 WebSocket URL 后，再以该 URL 为参数启动 `codex-tui` 终端界面。脚本处理了服务器启动超时检测、进程清理和错误日志输出等边界情况，适合本地调试和开发测试使用。

## 关键函数

| 函数 | 说明 |
|------|------|
| `cleanup()` | 退出时终止 exec-server 进程并清理临时目录 |
| 主流程（无函数封装） | 启动 exec-server -> 轮询等待 WebSocket URL -> 设置环境变量 -> 启动 codex-tui |

## 使用方式

```bash
# 直接运行，可追加传递给 codex-tui 的参数
./scripts/run_tui_with_exec_server.sh [codex-tui 参数...]

# 自定义 exec-server 监听地址
CODEX_EXEC_SERVER_LISTEN_URL=ws://127.0.0.1:9000 ./scripts/run_tui_with_exec_server.sh

# 自定义启动超时时间（秒）
CODEX_EXEC_SERVER_START_TIMEOUT_SECONDS=60 ./scripts/run_tui_with_exec_server.sh
```

## 实现备注

- 默认监听地址为 `ws://127.0.0.1:0`（端口 0 表示由操作系统自动分配可用端口）。
- 使用 50ms 间隔轮询 exec-server 的标准输出，等待其打印以 `ws://` 开头的 URL，默认超时 120 秒。
- 通过 `trap` 捕获 `EXIT`、`INT`、`TERM`、`HUP` 信号确保清理。
- exec-server 的标准输出和错误输出分别重定向到临时文件，启动失败时将日志内容输出到 stderr 便于排查。
- `codex-tui` 启动时附带 `-c mcp_oauth_credentials_store=file` 配置参数。
