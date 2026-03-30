# `config.rs` — 指标客户端配置

## 功能概述
该文件定义了 `MetricsConfig` 结构体和 `MetricsExporter` 枚举，提供指标客户端的完整配置选项。支持 OTLP 和内存两种导出器模式，可配置导出间隔、运行时读取器和默认标签。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `MetricsExporter` | 枚举 | 指标导出器类型：Otlp 或 InMemory |
| `MetricsConfig` | 结构体 | 指标配置：环境、服务名、版本、导出器、间隔、默认标签等 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `MetricsConfig::otlp` | `(env, service, version, exporter) -> Self` | 创建 OTLP 配置 |
| `MetricsConfig::in_memory` | `(env, service, version, exporter) -> Self` | 创建内存配置（测试用） |
| `with_export_interval` | `(self, interval) -> Self` | 设置导出间隔 |
| `with_runtime_reader` | `(self) -> Self` | 启用运行时快照读取器 |
| `with_tag` | `(self, key, value) -> Result<Self>` | 添加校验过的默认标签 |

## 依赖关系
- **引用的 crate**: `opentelemetry_sdk`
- **被引用方**: `MetricsClient::new`

## 实现备注
- 默认标签使用 `BTreeMap` 保证键有序
