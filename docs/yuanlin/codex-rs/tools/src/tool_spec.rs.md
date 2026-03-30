# `tool_spec.rs` -- 工具规格与 Responses API 序列化

## 功能概述
定义 `ToolSpec` 枚举，代表 OpenAI Responses API 支持的所有工具类型：Function、ToolSearch、LocalShell、ImageGeneration、WebSearch 和 Freeform（自定义）。提供 `ConfiguredToolSpec` 用于携带并行调用支持标志，以及 `create_tools_json_for_responses_api` 批量序列化为 API 兼容 JSON。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ToolSpec` | enum | 工具规格枚举，6 种变体对应不同 API 工具类型 |
| `ConfiguredToolSpec` | struct | 带并行调用支持标志的工具规格 |
| `ResponsesApiWebSearchFilters` | struct | Web 搜索域名过滤器 |
| `ResponsesApiWebSearchUserLocation` | struct | Web 搜索用户位置信息 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ToolSpec::name` | `fn(&self) -> &str` | 获取工具名称 |
| `create_tools_json_for_responses_api` | `fn(tools) -> Result<Vec<Value>>` | 批量序列化为 Responses API JSON |

## 依赖关系
- **引用的 crate**: `serde`, `codex_protocol`
- **被引用方**: codex core 构建 API 请求
