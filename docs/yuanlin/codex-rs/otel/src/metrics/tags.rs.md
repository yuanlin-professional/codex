# `tags.rs` — 会话级指标标签值构建

## 功能概述
该文件定义了会话级指标标签的常量和 `SessionMetricTagValues` 结构体，用于将认证模式、会话来源、发起者、服务名、模型和应用版本等元数据组装为标签列表。每个标签在构建时都会经过键/值校验。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `SessionMetricTagValues` | 结构体 | 会话级标签值容器，持有 auth_mode/session_source/originator/model 等 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `into_tags` | `(self) -> Result<Vec<(&str, &str)>>` | 将结构体转换为校验过的标签列表 |

## 依赖关系
- **引用的 crate**: 无外部依赖
- **被引用方**: `SessionTelemetry::metadata_tag_refs`

## 实现备注
- 可选标签（如 auth_mode, service_name）在 None 时被跳过
- 内含单元测试验证标签顺序和可选字段跳过行为
