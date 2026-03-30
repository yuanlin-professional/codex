# `client.rs` — ChatGPT 后端 HTTP 客户端

## 功能概述

与 ChatGPT 后端 API 通信的 HTTP 客户端。支持从 CodexAuth 构造认证客户端，执行云任务相关的 API 调用（列表任务、获取详情、获取 diff、创建任务、获取配置需求文件等）。处理分页、错误响应和超时。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `Client` | struct | ChatGPT 后端 HTTP 客户端 |
| `RequestError` | enum | 请求错误类型 |

## 依赖关系

- **引用的 crate**: `reqwest`、`codex_core`（auth）
- **被引用方**: `cloud-tasks-client`、`cloud-requirements`
