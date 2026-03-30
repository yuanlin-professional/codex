# app-server-test-client

## 功能概述

`codex-app-server-test-client` 是一个用于测试和调试 Codex app-server 的命令行客户端工具。它提供了一个最小化但功能完整的客户端实现，支持通过 stdio（子进程）或 WebSocket 两种传输方式与 app-server 交互，涵盖了 app-server 协议的所有主要操作。

主要功能包括：

- **服务器管理**：通过 `serve` 子命令在后台启动 app-server WebSocket 服务。
- **消息发送**：通过 V1 (`send-message`) 和 V2 (`send-message-v2`) 协议发送用户消息并流式接收响应。
- **线程管理**：支持创建线程、恢复线程、列出线程等操作。
- **审批流程测试**：提供专门的子命令来触发和测试命令执行审批 (`trigger-cmd-approval`) 和文件变更审批 (`trigger-patch-approval`) 流程。
- **多命令审批测试**：`trigger-zsh-fork-multi-cmd-approval` 测试 zsh fork 场景下的多子命令审批行为。
- **登录流程测试**：`test-login` 支持 ChatGPT 浏览器回调和设备码两种登录方式。
- **实时流观察**：`watch` 模式持续接收并显示所有服务器通知/事件。
- **暂停超时测试**：`live-elicitation-timeout-pause` 验证 elicitation 暂停能正确挂起统一执行超时。
- **OpenTelemetry 追踪集成**：自动附加 W3C trace context 并输出 trace URL。

## 架构说明

该工具采用同步 JSON-RPC 客户端设计（基于阻塞式 I/O），内部结构如下：

```
CLI (clap) --> CliCommand enum --> with_client() --> CodexClient
                                                        |
                                        +---------+----------+
                                        |                    |
                                 ClientTransport::Stdio  ClientTransport::WebSocket
                                  (子进程 stdin/stdout)    (tungstenite WebSocket)
```

- **Endpoint 解析**：根据 `--codex-bin`（子进程 stdio 模式）或 `--url`（WebSocket 模式）选择传输方式。
- **CodexClient**：核心客户端实现，封装了所有 JSON-RPC 交互逻辑，包括请求发送、响应等待、通知缓冲、服务器请求处理。
- **审批处理**：客户端内置命令执行和文件变更审批自动响应逻辑，支持配置 AlwaysAccept 或指定第 N 次拒绝。
- **动态工具**：支持通过 `--dynamic-tools` 传入 JSON 格式的工具规格定义。

## 目录结构

```
app-server-test-client/
├── Cargo.toml
└── src/
    ├── main.rs      # 入口：创建 tokio 单线程运行时并调用 lib
    └── lib.rs       # 核心逻辑：CLI 定义、CodexClient 实现、
                     #   所有子命令处理、审批逻辑、tracing 集成
```

## 依赖关系

| 依赖 | 说明 |
|------|------|
| `codex-app-server-protocol` | JSON-RPC 协议类型定义 |
| `codex-core` | 配置加载 |
| `codex-protocol` | 公共协议类型（ReasoningEffort、W3cTraceContext 等） |
| `codex-otel` | OpenTelemetry tracing 提供者 |
| `codex-utils-cli` | CLI 配置覆盖解析 |
| `clap` | 命令行参数解析（支持子命令、环境变量） |
| `tungstenite` | 同步 WebSocket 客户端 |
| `serde` / `serde_json` | 序列化 |
| `tokio` | 异步运行时 |
| `tracing` / `tracing-subscriber` | 日志和追踪 |
| `url` | URL 解析 |
| `uuid` | 请求 ID 生成 |

## 核心接口/API

### CLI 子命令

```
codex-app-server-test-client <COMMAND>

COMMANDS:
  serve                        启动后台 WebSocket app-server
  send-message                 发送 V1 用户消息
  send-message-v2              发送 V2 用户消息
  resume-message-v2            恢复已有线程并发送消息
  thread-resume                恢复线程并持续流式接收
  watch                        初始化并持续监听所有消息
  trigger-cmd-approval         触发命令执行审批流程
  trigger-patch-approval       触发文件变更审批流程
  no-trigger-cmd-approval      验证不触发审批的场景
  send-follow-up-v2            连续发送两个 turn（测试追问行为）
  trigger-zsh-fork-multi-cmd-approval  测试多子命令审批
  test-login                   测试 ChatGPT 登录流程
  get-account-rate-limits      获取账户速率限制
  model-list                   列出可用模型
  thread-list                  列出已存储线程
  thread-increment-elicitation 增加 elicitation 暂停计数
  thread-decrement-elicitation 减少 elicitation 暂停计数
  live-elicitation-timeout-pause  验证超时暂停功能
```

### 公共 API（库接口）

```rust
// 主入口（异步）
pub async fn run() -> Result<()>;

// V2 消息发送（供外部调用）
pub async fn send_message_v2(
    codex_bin: &Path,
    config_overrides: &[String],
    user_message: String,
    dynamic_tools: &Option<Vec<DynamicToolSpec>>,
) -> Result<()>;
```
