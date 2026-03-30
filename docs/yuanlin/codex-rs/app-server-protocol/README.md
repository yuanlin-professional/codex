# app-server-protocol

## 功能概述

`codex-app-server-protocol` 定义了 Codex app-server 与客户端之间的完整 JSON-RPC 协议类型系统。它是所有 app-server 通信的协议层基石，包含请求/响应/通知的类型定义、JSON-RPC 消息封装、实验性 API 标记系统，以及 TypeScript/JSON Schema 导出工具。

主要功能包括：

- **JSON-RPC 协议消息类型**：定义 `JSONRPCRequest`、`JSONRPCResponse`、`JSONRPCNotification`、`JSONRPCError` 等底层消息类型。
- **客户端请求/服务器通知**：定义 `ClientRequest`（如 Initialize、ThreadStart、TurnStart、GetAccount 等）和 `ServerNotification`（如 AgentMessageDelta、TurnCompleted、ItemCompleted 等）。
- **服务器请求**：定义 `ServerRequest`（如 CommandExecutionRequestApproval、FileChangeRequestApproval 等）。
- **线程/回合模型**：V2 协议中的 Thread、Turn、ThreadItem 等核心数据模型。
- **实验性 API 标记**：通过 `ExperimentalApi` trait 和 `#[experimental]` 属性标记实验性字段和变体。
- **Schema 导出**：支持生成 TypeScript 类型定义和 JSON Schema fixture，用于跨语言类型安全。
- **线程历史管理**：Thread history 相关类型定义。

## 架构说明

协议层采用分模块组织，将 V1 和 V2 版本的协议分离：

```
lib.rs
 ├── jsonrpc_lite    # 轻量级 JSON-RPC 消息类型（Request/Response/Notification/Error）
 ├── experimental_api # ExperimentalApi trait 和 ExperimentalField 注册机制
 ├── export          # TypeScript/JSON Schema 导出工具
 ├── schema_fixtures # Schema fixture 生成和读取
 └── protocol/
     ├── common      # 公共类型（RequestId、SandboxPolicy、AskForApproval 等）
     ├── v1          # V1 协议类型（Initialize、Profile、SandboxSettings 等）
     ├── v2          # V2 协议类型（Thread/Turn CRUD、通知、审批流程等）
     ├── mappers     # V1 <-> V2 类型映射
     ├── serde_helpers # 序列化辅助函数
     └── thread_history # 线程历史相关类型
```

所有协议类型都实现了 `serde::Serialize` / `serde::Deserialize`，并通过 `schemars::JsonSchema` 生成 JSON Schema。实验性字段使用 `codex-experimental-api-macros` 提供的 `#[derive(ExperimentalApi)]` 宏进行标记，在运行时可检查某个请求/通知是否使用了实验性功能。

类型通过 `ts-rs` crate 支持 TypeScript 类型导出，确保前端与 Rust 后端之间的类型一致性。

## 目录结构

```
app-server-protocol/
├── Cargo.toml
└── src/
    ├── lib.rs                # 模块声明和公共导出
    ├── jsonrpc_lite.rs       # JSON-RPC 消息类型定义
    ├── experimental_api.rs   # ExperimentalApi trait 和字段注册
    ├── export.rs             # TypeScript/JSON Schema 导出工具
    ├── schema_fixtures.rs    # Schema fixture 生成/读取
    ├── protocol/
    │   ├── mod.rs            # 协议子模块声明
    │   ├── common.rs         # 公共协议类型
    │   ├── v1.rs             # V1 协议类型
    │   ├── v2.rs             # V2 协议类型（Thread/Turn 模型）
    │   ├── mappers.rs        # V1/V2 类型映射
    │   ├── serde_helpers.rs  # 序列化辅助
    │   └── thread_history.rs # 线程历史类型
    └── bin/
        ├── export.rs             # Schema 导出 CLI
        └── write_schema_fixtures.rs  # Fixture 写入 CLI
```

## 依赖关系

| 依赖 | 说明 |
|------|------|
| `codex-protocol` | 底层协议公共类型 |
| `codex-experimental-api-macros` | `#[derive(ExperimentalApi)]` 过程宏 |
| `codex-git-utils` | Git SHA 类型 |
| `codex-utils-absolute-path` | 绝对路径工具 |
| `schemars` | JSON Schema 生成 |
| `serde` / `serde_json` | 序列化 |
| `serde_with` | 高级序列化辅助 |
| `ts-rs` | TypeScript 类型导出 |
| `clap` | CLI 参数解析（用于导出工具） |
| `rmcp` | MCP 协议类型（server 特性） |
| `inventory` | 静态注册实验性字段 |
| `uuid` | 唯一标识符生成 |
| `strum_macros` | 枚举宏 |
| `thiserror` | 错误类型派生 |

## 核心接口/API

### JSON-RPC 消息类型

```rust
pub enum JSONRPCMessage {
    Request(JSONRPCRequest),
    Response(JSONRPCResponse),
    Error(JSONRPCError),
    Notification(JSONRPCNotification),
}

pub struct JSONRPCRequest {
    pub id: RequestId,
    pub method: String,
    pub params: Option<Value>,
    pub trace: Option<W3cTraceContext>,
}
```

### 客户端请求（ClientRequest）

```rust
pub enum ClientRequest {
    Initialize { request_id, params: InitializeParams },
    ThreadStart { request_id, params: ThreadStartParams },
    TurnStart { request_id, params: TurnStartParams },
    ThreadRead { request_id, params: ThreadReadParams },
    GetAccount { request_id, params: GetAccountParams },
    // ... 更多请求类型
}
```

### 服务器通知（ServerNotification）

```rust
pub enum ServerNotification {
    AgentMessageDelta(AgentMessageDeltaNotification),
    ItemStarted(ItemStartedNotification),
    ItemCompleted(ItemCompletedNotification),
    TurnStarted(TurnStartedNotification),
    TurnCompleted(TurnCompletedNotification),
    CommandExecutionOutputDelta(CommandExecutionOutputDeltaNotification),
    // ... 更多通知类型
}
```

### 实验性 API

```rust
pub trait ExperimentalApi {
    fn experimental_reason(&self) -> Option<&'static str>;
}
```
