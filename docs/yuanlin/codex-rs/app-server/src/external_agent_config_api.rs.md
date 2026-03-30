# `external_agent_config_api.rs` — 外部代理配置检测与导入 API

## 功能概述

本模块实现了 `ExternalAgentConfigApi` 结构体，提供外部代理配置的检测（detect）和导入（import）功能。它封装了 `codex_core::external_agent_config::ExternalAgentConfigService`，负责在 app-server 协议类型与核心类型之间进行映射转换。detect 操作扫描指定目录查找可迁移的配置项（Config、Skills、AgentsMd、McpServerConfig），import 操作执行实际的配置导入。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ExternalAgentConfigApi` | 结构体 | 外部代理配置 API，持有 `ExternalAgentConfigService` |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn(codex_home) -> Self` | 构造函数 |
| `detect` | `async fn(&self, params) -> Result<DetectResponse, ...>` | 检测可迁移的外部代理配置项 |
| `import` | `async fn(&self, params) -> Result<ImportResponse, ...>` | 导入指定的外部代理配置项 |

## 依赖关系

- **引用的 crate**: `codex_core::external_agent_config`（`ExternalAgentConfigService`、迁移项类型）、`codex_app_server_protocol`
- **被引用方**: `message_processor.rs`（处理 `externalAgentConfig/detect` 和 `externalAgentConfig/import` 请求）

## 实现备注

- 迁移项类型映射（`Config`、`Skills`、`AgentsMd`、`McpServerConfig`）在核心类型和协议类型之间一一对应。
