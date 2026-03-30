# `network_policy.rs` -- 网络策略决策引擎与审计日志

## 功能概述
网络策略决策的核心引擎，组合基线策略（allowlist/denylist）和外部决策器（`NetworkPolicyDecider` trait）的结果，并通过 tracing 发出结构化审计事件（OTel 格式）。审计事件包含会话 ID、用户信息、协议、决策来源和策略覆盖标志等字段。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `NetworkProtocol` | enum | 网络协议：Http / HttpsConnect / Socks5Tcp / Socks5Udp |
| `NetworkPolicyDecision` | enum | 策略决策：Deny / Ask |
| `NetworkDecisionSource` | enum | 决策来源：BaselinePolicy / ModeGuard / ProxyState / Decider |
| `NetworkDecision` | enum | 最终决策：Allow 或 Deny（附原因、来源和决策类型） |
| `NetworkPolicyRequest` | struct | 策略请求，包含协议、主机、端口、客户端地址等 |
| `NetworkPolicyDecider` | trait | 外部策略决策器接口 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `evaluate_host_policy` | `async fn(state, decider, request) -> Result<NetworkDecision>` | 评估主机策略：基线拒绝 -> 外部决策器覆盖 -> 审计 |
| `emit_block_decision_audit_event` | `fn(state, args)` | 发出非域名策略阻断审计事件 |
| `emit_allow_decision_audit_event` | `fn(state, args)` | 发出非域名策略允许审计事件 |

## 依赖关系
- **引用的 crate**: `async_trait`, `chrono`, `tracing`
- **被引用方**: `http_proxy.rs`, `socks5.rs`

## 实现备注
- 审计事件 target 为 `codex_otel.network_proxy`
- not_allowed_local 和 denied 原因不被外部决策器覆盖
- 仅 not_allowed 原因会咨询外部决策器
