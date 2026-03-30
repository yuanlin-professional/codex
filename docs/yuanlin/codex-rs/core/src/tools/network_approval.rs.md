# `network_approval.rs` — 网络访问审批服务

## 功能概述
实现了网络访问的内联审批机制。当工具执行触发被沙箱策略阻止的网络请求时，该服务可以暂停执行并请求用户审批（允许/拒绝/会话级允许）。支持按主机+协议+端口的去重审批、会话级缓存、Guardian 审批路由以及网络策略修改持久化。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `NetworkApprovalService` | struct | 网络审批服务：管理活跃调用、审批结果、待处理审批、会话缓存 |
| `NetworkApprovalMode` | enum | 审批模式：Immediate（同步等待）/ Deferred（延迟处理） |
| `NetworkApprovalSpec` | struct | 审批规格：network + mode |
| `HostApprovalKey` | struct | 主机审批键：host + protocol + port |
| `PendingApprovalDecision` | enum | 待处理决策：AllowOnce / AllowForSession / Deny |
| `NetworkApprovalOutcome` | enum | 审批结果：DeniedByUser / DeniedByPolicy(String) |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `handle_inline_policy_request` | `async fn(&self, session, request) -> NetworkDecision` | 内联策略请求处理：缓存检查 -> 去重 -> 审批 -> 缓存结果 |
| `begin_network_approval` | `async fn(session, turn_id, has_managed_network, spec) -> Option<ActiveNetworkApproval>` | 开始网络审批：注册调用 |
| `finish_immediate_network_approval` | `async fn(session, active) -> Result<(), ToolError>` | 完成 Immediate 模式的审批，检查结果 |
| `build_network_policy_decider` | `fn(network_approval, session) -> Arc<dyn NetworkPolicyDecider>` | 构建网络策略决策器闭包 |

## 依赖关系
- **引用的 crate**: `codex_network_proxy`, `codex_protocol`, `indexmap`, `uuid`
- **内部依赖**: `crate::guardian`, `crate::network_policy_decision`
- **被引用方**: `orchestrator.rs`

## 实现备注
- 使用 `PendingHostApproval` + `Notify` 实现相同主机请求的去重等待
- `AllowForSession` 会缓存到 `session_approved_hosts`，后续请求自动放行
- 用户拒绝（DeniedByUser）的优先级高于策略拒绝（DeniedByPolicy），不可被覆盖
- 网络策略修改通过 `persist_network_policy_amendment` 持久化
