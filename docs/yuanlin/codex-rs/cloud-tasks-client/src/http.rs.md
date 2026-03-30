# `http.rs` — 云任务 HTTP 客户端实现

## 功能概述

实现 `CloudBackend` trait 的 HTTP 客户端版本。通过 ChatGPT 后端 API 与云任务服务通信，支持任务列表分页、获取任务详情/diff/消息、列出兄弟尝试（best-of-N）、预检和应用任务补丁、创建新任务等操作。使用 `codex_backend_client` 进行认证和 HTTP 请求。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `HttpClient` | struct | 基于 HTTP 的 CloudBackend 实现 |

## 依赖关系

- **引用的 crate**: `codex_backend_client`、`codex_git_utils`、`chrono`、`serde`
- **被引用方**: `cloud-tasks-client/src/lib.rs`（online feature）
