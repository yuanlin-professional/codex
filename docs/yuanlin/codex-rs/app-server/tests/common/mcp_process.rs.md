# `mcp_process.rs` — MCP 子进程测试驱动器

## 功能概述
提供 `McpProcess` 结构体，用于在集成测试中启动和管理 `codex-app-server` 子进程。
通过 stdin/stdout 管道与子进程进行 JSON-RPC 通信。封装了完整的初始化握手流程、
各种 JSON-RPC 请求方法（thread/start、turn/start、model/list 等数十种），以及消息读取和缓冲机制。
支持自定义环境变量和命令行参数。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `McpProcess` | struct | MCP 测试进程管理器，包含 stdin/stdout 管道、请求 ID 计数器和消息缓冲队列 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `pub async fn new(codex_home: &Path) -> Result<Self>` | 启动新的 codex-app-server 子进程 |
| `new_with_env` | `pub async fn new_with_env(codex_home: &Path, env_overrides: &[...]) -> Result<Self>` | 带环境变量覆盖启动子进程 |
| `initialize` | `pub async fn initialize(&mut self) -> Result<()>` | 执行初始化握手 |
| `send_turn_start_request` | `pub async fn send_turn_start_request(&mut self, params) -> Result<i64>` | 发送 turn/start 请求 |
| `send_thread_start_request` | `pub async fn send_thread_start_request(&mut self, params) -> Result<i64>` | 发送 thread/start 请求 |
| `read_stream_until_response_message` | `pub async fn read_stream_until_response_message(&mut self, id) -> Result<JSONRPCResponse>` | 读取直到匹配指定 ID 的响应 |
| `read_stream_until_notification_message` | `pub async fn read_stream_until_notification_message(&mut self, method) -> Result<JSONRPCNotification>` | 读取直到匹配指定方法的通知 |
| `interrupt_turn_and_wait_for_aborted` | `pub async fn interrupt_turn_and_wait_for_aborted(&mut self, ...) -> Result<()>` | 中断运行中的 turn 并等待终止确认 |

## 依赖关系
- **引用的 crate**: `tokio`, `anyhow`, `codex_app_server_protocol`, `codex_core`, `codex_utils_cargo_bin`, `serde_json`
- **被引用方**: 几乎所有集成测试通过 `McpProcess` 驱动 app-server

## 实现备注
- 使用 `kill_on_drop(true)` 确保子进程在 `McpProcess` 被丢弃时终止
- `Drop` 实现中执行同步清理：先关闭 stdin 请求优雅关闭，然后轮询退出，最后强制 kill
- 消息缓冲机制 `pending_messages` 用于处理乱序到达的消息
- 子进程的 stderr 通过后台任务转发到测试输出
