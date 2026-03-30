# `responses.rs` -- HTTP 响应构建工具

## 功能概述
为网络代理提供标准化的 HTTP 响应构建函数，包括纯文本响应、JSON 响应、阻断响应（包含 `x-proxy-error` 头部）和策略决策详情响应。阻断消息根据原因类型（not_allowed/denied/method_not_allowed/mitm_required）返回人类可读的说明。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `PolicyDecisionDetails` | struct | 策略决策详情，包含决策、原因、来源、协议、主机和端口 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `text_response` | `fn(status, body) -> Response` | 构建纯文本响应 |
| `json_response` | `fn(value) -> Response` | 构建 JSON 响应 |
| `blocked_text_response` | `fn(reason) -> Response` | 构建 403 阻断响应 |
| `blocked_header_value` | `fn(reason) -> &'static str` | 根据原因返回 x-proxy-error 头部值 |
| `blocked_message` | `fn(reason) -> &'static str` | 根据原因返回人类可读的阻断消息 |

## 依赖关系
- **引用的 crate**: `rama_http`, `serde`
- **被引用方**: `http_proxy.rs`, `socks5.rs`, `mitm.rs`
