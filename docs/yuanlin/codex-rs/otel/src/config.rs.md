# `config.rs` — OTEL 全局配置与导出器定义

## 功能概述
该文件定义了 Codex OTEL 子系统的全局配置类型。包括 `OtelSettings`（环境、服务名、版本、codex_home 和各类导出器配置）、`OtelExporter` 枚举（None/Statsig/OtlpGrpc/OtlpHttp）、`OtelHttpProtocol` 和 `OtelTlsConfig`。`resolve_exporter` 函数将 Statsig 导出器解析为具体的 OTLP HTTP 端点配置。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `OtelSettings` | 结构体 | 完整的 OTEL 配置：环境、服务名、导出器、运行时指标开关 |
| `OtelExporter` | 枚举 | 导出器类型：None/Statsig/OtlpGrpc/OtlpHttp |
| `OtelHttpProtocol` | 枚举 | HTTP 协议：Binary（protobuf）或 Json |
| `OtelTlsConfig` | 结构体 | TLS 配置：CA 证书、客户端证书和私钥路径 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `resolve_exporter` | `(exporter: &OtelExporter) -> OtelExporter` | 将 Statsig 解析为 OTLP HTTP 配置（测试时返回 None） |

## 依赖关系
- **引用的 crate**: `codex_utils_absolute_path`
- **被引用方**: `provider.rs`, `metrics/client.rs`

## 实现备注
- Statsig 导出器使用内置的 API Key 和 OTLP HTTP 端点
- 测试或禁用默认导出器 feature 时 Statsig 解析为 None
