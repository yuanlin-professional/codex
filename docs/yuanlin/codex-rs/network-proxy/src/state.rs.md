# `state.rs` -- 网络代理配置状态与约束验证

## 功能概述
管理网络代理配置的构建和约束验证。`build_config_state` 将配置编译为运行时可用的 GlobSet 和 MITM 状态。`validate_policy_against_constraints` 验证用户配置是否满足管理方（managed config）设置的约束，包括域名允许/拒绝列表的子集关系、网络模式的收紧限制、Unix 套接字权限等。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `NetworkProxyConstraints` | struct | 管理方约束，定义配置的允许边界 |
| `NetworkProxyConstraintError` | enum | 约束违反错误 |
| `PartialNetworkProxyConfig` | struct | 部分网络配置，用于增量更新 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `build_config_state` | `fn(config, constraints) -> Result<ConfigState>` | 编译配置为运行时状态 |
| `validate_policy_against_constraints` | `fn(config, constraints) -> Result<(), NetworkProxyConstraintError>` | 验证配置满足约束 |

## 依赖关系
- **引用的 crate**: `globset`, `serde`
- **被引用方**: `runtime.rs` 在重载配置时调用

## 实现备注
- 约束验证支持三种 allowlist 模式：子集（默认）、仅扩展、完全匹配
- 全局通配符 `*` 在管理方 allowed_domains 约束中被禁止
- denylist 约束同样支持固定（exact match）和可扩展两种模式
