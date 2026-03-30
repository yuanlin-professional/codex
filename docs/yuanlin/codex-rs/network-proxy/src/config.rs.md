# `config.rs` -- 网络代理配置定义

## 功能概述
定义网络代理的完整配置模型，包括代理设置（启用/禁用、监听地址、SOCKS5、上游代理、MITM、网络模式）、域名权限（允许/拒绝）和 Unix 套接字权限。提供域名权限的有效条目计算（deny 优先于 allow）、绑定地址解析（包括 URL/host:port/IPv6 字面量）以及非回环绑定的安全钳制。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `NetworkProxyConfig` | struct | 顶层配置 |
| `NetworkProxySettings` | struct | 网络代理设置，包含所有配置项 |
| `NetworkMode` | enum | 网络模式：Limited（只读）或 Full（完全访问） |
| `NetworkDomainPermission` | enum | 域名权限：None / Allow / Deny |
| `NetworkDomainPermissions` | struct | 域名权限集合，支持序列化为有效条目映射 |
| `ValidatedUnixSocketPath` | enum | 已验证的 Unix 套接字路径：Native 或 UnixStyleAbsolute |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `resolve_runtime` | `fn(cfg) -> Result<RuntimeConfig>` | 解析配置为运行时地址 |
| `clamp_bind_addrs` | `fn(http_addr, socks_addr, cfg) -> (SocketAddr, SocketAddr)` | 钳制非回环绑定地址 |
| `NetworkProxySettings::upsert_domain_permission` | `fn(&mut self, host, permission, normalize)` | 更新域名权限，移除相反权限的条目 |

## 依赖关系
- **引用的 crate**: `serde`, `url`, `codex_utils_absolute_path`
- **被引用方**: 几乎所有 network-proxy 模块

## 实现备注
- 域名权限优先级：None < Allow < Deny，重复模式取最高优先级
- 当 Unix 套接字代理启用时，强制回环绑定（覆盖 dangerously_allow_non_loopback_proxy）
- 默认 HTTP 代理端口 3128，SOCKS5 端口 8081
