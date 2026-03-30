# `network_proxy_spec.rs` -- 网络代理规格配置与启动

## 功能概述
`NetworkProxySpec` 结构体封装了网络代理的配置（`NetworkProxyConfig`）和约束（`NetworkProxyConstraints`），负责将用户配置与管理员需求合并，验证策略合规性，并启动网络代理服务。支持域名白名单/黑名单的分层合并策略、sandbox 策略感知的列表扩展控制，以及执行策略（exec policy）的网络规则叠加。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `NetworkProxySpec` | struct | 网络代理完整规格，包含 config、constraints 和 hard_deny 标志 |
| `StartedNetworkProxy` | struct | 已启动的网络代理实例，持有 `NetworkProxy` 和 `NetworkProxyHandle` |
| `StaticNetworkProxyReloader` | struct (private) | 静态配置的 reload 实现，`maybe_reload` 始终返回 `None` |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `NetworkProxySpec::from_config_and_constraints` | `fn(config, requirements, sandbox_policy) -> Result<Self>` | 从配置和约束创建规格，合并白名单/黑名单 |
| `NetworkProxySpec::start_proxy` | `async fn(&self, ...) -> Result<StartedNetworkProxy>` | 启动网络代理，支持策略审批流和阻止请求观察者 |
| `NetworkProxySpec::with_exec_policy_network_rules` | `fn(&self, exec_policy) -> Result<Self>` | 叠加执行策略中的网络域名规则 |
| `NetworkProxySpec::apply_requirements` | `fn(config, requirements, sandbox_policy, hard_deny) -> (Config, Constraints)` | 将管理员需求应用到配置并生成约束 |
| `apply_exec_policy_network_rules` | `fn(config, exec_policy)` | 从执行策略中提取 allow/deny 域名并注入代理配置 |

## 依赖关系
- **引用的 crate**: `codex_network_proxy`, `codex_execpolicy`, `codex_protocol`, `async_trait`
- **引用的模块**: `crate::config_loader::NetworkConstraints`
- **被引用方**: `config/mod.rs` 中构建网络代理配置时使用

## 实现备注
- 白名单扩展仅在 `ReadOnly` 或 `WorkspaceWrite` sandbox 模式下启用（即用户可在管理员白名单基础上添加域名），在 `DangerFullAccess` 模式下固定为管理员列表。
- `managed_allowed_domains_only` 标志为 `true` 时，白名单严格锁定为管理员定义的域名，用户添加的域名被忽略，且白名单外的请求被硬拒绝（hard deny）而非转入审批流。
- 合并域名列表时使用大小写不敏感比较，避免重复条目。
