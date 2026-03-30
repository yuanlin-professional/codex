# `network_policy_decision.rs` — 网络策略决策处理与审批上下文

## 功能概述

此文件处理网络代理的策略决策，包括解析网络策略决策载荷、构建网络审批上下文、生成被拒绝请求的用户消息，以及将网络策略修正转换为执行策略规则。它是网络权限控制中从代理层决策到用户交互再到策略持久化的桥接层。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `NetworkPolicyDecisionPayload` | 结构体 | 网络策略决策载荷，包含决策结果、来源、协议、主机、原因和端口 |
| `ExecPolicyNetworkRuleAmendment` | 结构体 | 执行策略网络规则修正，包含协议、决策和理由 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `network_approval_context_from_payload` | `(payload) -> Option<NetworkApprovalContext>` | 从决策载荷构建审批上下文（仅处理来自 Decider 的 Ask 决策） |
| `denied_network_policy_message` | `(blocked) -> Option<String>` | 为被拒绝的网络请求生成人类可读的消息 |
| `execpolicy_network_rule_amendment` | `(amendment, context, host) -> ExecPolicyNetworkRuleAmendment` | 将用户审批修正转换为执行策略规则 |

## 依赖关系

- **引用的 crate**: `codex_execpolicy`（`Decision`、`NetworkRuleProtocol`）、`codex_network_proxy`、`codex_protocol`（审批类型）
- **被引用方**: 网络权限审批流程和 shell 工具执行使用

## 实现备注

- 拒绝原因有明确的描述映射：`denied`（显式拒绝）、`not_allowed`（不在允许列表）、`not_allowed_local`（本地地址被阻止）等
- 协议映射：`Http` -> Http、`Https` -> Https、`Socks5Tcp` -> Socks5Tcp、`Socks5Udp` -> Socks5Udp
- 审批上下文仅在 `decision == Ask && source == Decider` 时构建
- 主机名为空时不构建审批上下文
