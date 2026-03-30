# `client.rs` — OpenTelemetry 指标客户端实现

## 功能概述
`MetricsClient` 是 Codex 的 OpenTelemetry 指标客户端核心实现。基于 `SdkMeterProvider` 构建，支持计数器、直方图和持续时间直方图的发送，带有标签合并和校验。支持 OTLP gRPC/HTTP 和内存导出器，可选的 `ManualReader` 用于运行时快照。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `MetricsClient` | 结构体 | 线程安全的指标客户端（`Arc<MetricsClientInner>`） |
| `MetricsClientInner` | 结构体(内部) | 内部状态：meter provider、计数器/直方图缓存、默认标签 |
| `SharedManualReader` | 结构体(内部) | 可克隆的 ManualReader 包装，用于运行时快照 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `MetricsClient::new` | `(config: MetricsConfig) -> Result<Self>` | 从配置构建客户端，校验默认标签 |
| `counter` | `(&self, name, inc, tags) -> Result<()>` | 发送计数器增量 |
| `histogram` | `(&self, name, value, tags) -> Result<()>` | 发送直方图采样 |
| `record_duration` | `(&self, name, duration, tags) -> Result<()>` | 记录持续时间（毫秒）到直方图 |
| `snapshot` | `(&self) -> Result<ResourceMetrics>` | 收集运行时指标快照 |
| `shutdown` | `(&self) -> Result<()>` | 刷新并停止指标 provider |

## 依赖关系
- **引用的 crate**: `opentelemetry`, `opentelemetry_sdk`, `opentelemetry_otlp`, `os_info`
- **被引用方**: `SessionTelemetry`, `OtelProvider`

## 实现备注
- 计数器和直方图按名称懒创建并缓存在 `Mutex<HashMap>` 中
- 默认标签与调用标签通过 `BTreeMap` 合并，调用标签覆盖同名默认
- 包含 OS 类型和版本作为资源属性
