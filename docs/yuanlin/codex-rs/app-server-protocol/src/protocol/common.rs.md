# `common.rs` — app-server 协议公共定义与宏

## 功能概述

app-server 协议的核心文件，通过声明式宏系统定义了完整的客户端-服务器双向通信协议。包含四组宏生成的枚举：`ClientRequest`（客户端请求，约 60 个方法）、`ClientResponse`（客户端响应）、`ServerRequest`（服务端请求，约 10 个方法）、`ServerNotification`（服务端通知，约 50 种）和 `ClientNotification`（客户端通知）。还定义了认证模式(`AuthMode`)、模糊文件搜索等公共类型。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `AuthMode` | 枚举 | 认证模式（ApiKey / Chatgpt / ChatgptAuthTokens） |
| `ClientRequest` | 枚举 | 所有客户端可发送的请求方法（Initialize、ThreadStart、TurnStart 等） |
| `ServerRequest` | 枚举 | 所有服务端向客户端发起的请求（CommandExecutionRequestApproval 等） |
| `ServerNotification` | 枚举 | 所有服务端通知（ThreadStarted、TurnCompleted、AgentMessageDelta 等） |
| `ClientNotification` | 枚举 | 客户端通知（Initialized） |
| `FuzzyFileSearchParams` | 结构体 | 模糊文件搜索参数 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ClientRequest::id` | `fn id(&self) -> &RequestId` | 获取请求 ID |
| `ClientRequest::method` | `fn method(&self) -> String` | 获取请求方法名 |
| `ServerNotification::to_params` | `fn to_params(self) -> Result<Value>` | 将通知转换为 JSON 参数 |
| `export_*` 系列函数 | - | 生成 TypeScript 和 JSON Schema |

## 依赖关系

- **引用的 crate**: `schemars`, `serde`, `strum_macros`, `ts_rs`, `codex_experimental_api_macros`, `codex_protocol`
- **被引用方**: `lib.rs` 通过 `pub use protocol::common::*` 导出所有内容

## 实现备注

- 使用四个声明式宏（`client_request_definitions!`、`server_request_definitions!`、`server_notification_definitions!`、`client_notification_definitions!`）自动生成枚举类型、实验性 API 检查、TypeScript 导出函数和 JSON Schema 导出函数。
- 实验性方法使用 `#[experimental("method/name")]` 属性标记，自动集成到 `ExperimentalApi` trait。
- 文件超过 1800 行（含大量测试），覆盖了序列化/反序列化、实验性标记检测等场景。
