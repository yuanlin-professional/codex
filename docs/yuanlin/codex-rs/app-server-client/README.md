# app-server-client

## 功能概述

`codex-app-server-client` 是 Codex 项目中用于与 app-server 通信的共享进程内/远程客户端门面（facade）库。它为 TUI 和 exec 等上层界面提供统一的异步 API，屏蔽了底层进程内（in-process）和远程 WebSocket 两种传输模式的差异。

主要功能包括：

- **运行时启动与初始化握手**：封装 `codex_app_server::in_process` 的启动流程，自动完成 initialize/initialized 能力协商。
- **类型化请求/通知分发**：支持 `request`（发送请求并等待响应）、`request_typed`（自动反序列化响应体）、`notify`（发送单向通知）。
- **服务器请求解析与拒绝**：处理来自服务器的请求（如命令执行审批），支持 `resolve_server_request` 和 `reject_server_request`。
- **事件消费与背压信号**：通过有界 `mpsc` 通道传递事件，在消费者落后时发出 `Lagged` 标记，同时保证关键转录事件（AgentMessageDelta、ItemCompleted、TurnCompleted 等）的无损传递。
- **有界优雅关闭**：支持带超时的优雅关闭，超时后自动中止 worker 任务。

## 架构说明

该 crate 采用 worker 任务 + 双通道架构：

```
调用方 (TUI/exec)
    |
    v
[command_tx] ---> Worker Task ---> [InProcessClientHandle / WebSocket]
    ^                  |
    |                  v
[event_rx]  <--- [event_tx]
```

- **InProcessAppServerClient**：进程内模式。通过 `tokio::spawn` 创建一个 worker 任务，桥接调用方的 `mpsc` 通道与底层 `InProcessClientHandle`。所有请求通过 `ClientCommand` 枚举分发，包括 Request、Notify、ResolveServerRequest、RejectServerRequest 和 Shutdown。
- **RemoteAppServerClient**：远程模式。通过 WebSocket (tokio-tungstenite) 连接到远程 app-server，完成 JSON-RPC 协议交互。
- **AppServerClient**：统一枚举，对外暴露一致的 API，内部根据 InProcess/Remote 模式分发。

事件流采用两级策略：
- **无损层**（Lossless）：AgentMessageDelta、PlanDelta、ItemCompleted、TurnCompleted 等事件在队列满时会阻塞等待消费者，保证不丢失。
- **尽力层**（Best-effort）：CommandExecutionOutputDelta、进度通知等在队列满时被丢弃，仅产生 Lagged 标记。

## 目录结构

```
app-server-client/
├── Cargo.toml
└── src/
    ├── lib.rs        # 核心逻辑：InProcessAppServerClient、AppServerClient、
    │                 #   事件转发、背压策略、类型化请求等
    └── remote.rs     # RemoteAppServerClient：WebSocket 远程传输实现
```

## 依赖关系

| 依赖 | 说明 |
|------|------|
| `codex-app-server` | 进程内 app-server 运行时 |
| `codex-app-server-protocol` | JSON-RPC 协议类型定义 |
| `codex-arg0` | argv0 分发路径 |
| `codex-core` | 配置、认证、加载器等核心功能 |
| `codex-feedback` | 遥测与反馈 |
| `codex-protocol` | 协议层公共类型（SessionSource 等） |
| `tokio` | 异步运行时（mpsc/oneshot/time） |
| `tokio-tungstenite` | 异步 WebSocket 客户端 |
| `serde` / `serde_json` | 序列化/反序列化 |
| `futures` | 异步流/Sink 扩展 |

## 核心接口/API

### InProcessAppServerClient

```rust
// 启动进程内客户端
pub async fn start(args: InProcessClientStartArgs) -> IoResult<Self>;

// 发送请求（原始 JSON-RPC 结果）
pub async fn request(&self, request: ClientRequest) -> IoResult<RequestResult>;

// 发送请求并反序列化响应
pub async fn request_typed<T: DeserializeOwned>(&self, request: ClientRequest) -> Result<T, TypedRequestError>;

// 发送通知
pub async fn notify(&self, notification: ClientNotification) -> IoResult<()>;

// 解析/拒绝服务器请求
pub async fn resolve_server_request(&self, request_id: RequestId, result: JsonRpcResult) -> IoResult<()>;
pub async fn reject_server_request(&self, request_id: RequestId, error: JSONRPCErrorError) -> IoResult<()>;

// 获取下一个事件
pub async fn next_event(&mut self) -> Option<InProcessServerEvent>;

// 优雅关闭
pub async fn shutdown(self) -> IoResult<()>;
```

### AppServerClient（统一门面）

```rust
pub enum AppServerClient {
    InProcess(InProcessAppServerClient),
    Remote(RemoteAppServerClient),
}

// 提供与 InProcessAppServerClient 相同的方法签名
// 内部按模式自动分发
```

### TypedRequestError

分层错误类型，区分传输错误、服务器 JSON-RPC 错误和响应反序列化错误：

```rust
pub enum TypedRequestError {
    Transport { method: String, source: IoError },
    Server { method: String, source: JSONRPCErrorError },
    Deserialize { method: String, source: serde_json::Error },
}
```
