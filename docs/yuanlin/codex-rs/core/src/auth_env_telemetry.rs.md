# `auth_env_telemetry.rs` — 认证环境变量遥测收集

## 功能概述

此文件收集与认证相关的环境变量存在性信息用于遥测上报。它检查 `OPENAI_API_KEY`、`CODEX_API_KEY` 和提供商自定义的 `env_key` 是否在环境中存在，以及是否启用了 API Key 环境变量支持。收集到的信息不包含实际的密钥值，只记录"是否存在"的布尔状态。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `AuthEnvTelemetry` | 结构体 | 认证环境遥测数据，包含各密钥环境变量的存在状态 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `collect_auth_env_telemetry` | `(provider, codex_api_key_env_enabled) -> AuthEnvTelemetry` | 收集认证环境遥测数据 |
| `AuthEnvTelemetry::to_otel_metadata` | `(&self) -> AuthEnvTelemetryMetadata` | 转换为 OTel 元数据格式 |
| `env_var_present` | `(name: &str) -> bool` | 检查环境变量是否存在且非空 |

## 依赖关系

- **引用的 crate**: `codex_otel`（`AuthEnvTelemetryMetadata`）、`crate::auth`（环境变量常量）、`crate::model_provider_info`
- **被引用方**: 会话初始化时收集遥测数据

## 实现备注

- `provider_env_key_name` 不输出实际的环境变量名（可能含敏感信息），而是固定输出 `"configured"` 字符串
- `env_var_present` 将 `NotUnicode` 错误也视为"存在"
- 空白字符串视为"不存在"（通过 `trim().is_empty()` 检查）
