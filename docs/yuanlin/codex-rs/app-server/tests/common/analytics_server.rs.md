# `analytics_server.rs` — 模拟分析事件服务器

## 功能概述
提供 `start_analytics_events_server` 异步函数，使用 `wiremock` 启动一个模拟 HTTP 服务器，
监听 `POST /codex/analytics-events/events` 端点并返回 200 状态码。用于集成测试中模拟分析事件上报的后端。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `start_analytics_events_server` | `pub async fn start_analytics_events_server() -> Result<MockServer>` | 启动模拟分析事件服务器，挂载 POST 端点并返回 MockServer 实例 |

## 依赖关系
- **引用的 crate**: `anyhow`, `wiremock`
- **被引用方**: `common/lib.rs` 中导出供测试套件使用
