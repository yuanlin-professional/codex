# `runtime.rs` -- 网络代理运行时状态管理

## 功能概述
管理网络代理的运行时状态，包括策略配置的热重载、域名允许/拒绝的动态更新、主机阻断决策评估、被阻断请求的遥测记录和缓冲、Unix 套接字权限检查等。核心是 `NetworkProxyState` 结构体，通过 `ConfigReloader` trait 实现配置动态加载，所有状态操作都是 reload-on-demand 的。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `NetworkProxyState` | struct | 代理状态主结构，持有配置、重载器和被阻断请求观察者 |
| `ConfigState` | struct | 编译后的配置状态，包含 GlobSet、MITM 状态和约束 |
| `HostBlockDecision` | enum | 主机阻断决策：Allowed 或 Blocked(原因) |
| `HostBlockReason` | enum | 阻断原因：Denied / NotAllowed / NotAllowedLocal |
| `BlockedRequest` | struct | 被阻断请求的遥测记录 |
| `ConfigReloader` | trait | 配置重载器 trait |
| `BlockedRequestObserver` | trait | 被阻断请求观察者 trait |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `host_blocked` | `async fn(&self, host, port) -> Result<HostBlockDecision>` | 综合评估主机是否被阻断（deny > local check > allowlist） |
| `add_allowed_domain` | `async fn(&self, host) -> Result<()>` | 动态添加允许域名（移除相同域名的拒绝条目） |
| `add_denied_domain` | `async fn(&self, host) -> Result<()>` | 动态添加拒绝域名（移除相同域名的允许条目） |
| `record_blocked` | `async fn(&self, entry) -> Result<()>` | 记录被阻断请求并通知观察者 |
| `force_reload` | `async fn(&self) -> Result<()>` | 强制重新加载配置 |
| `is_unix_socket_allowed` | `async fn(&self, path) -> Result<bool>` | 检查 Unix 套接字路径是否在允许列表中 |

## 依赖关系
- **引用的 crate**: `globset`, `tokio`, `time`, `anyhow`
- **被引用方**: `proxy.rs`, `http_proxy.rs`, `socks5.rs`, `mitm.rs`

## 实现备注
- 被阻断请求缓冲区最多 200 条（FIFO）
- DNS 查询超时 2 秒，失败时按非公网处理（防御 DNS 重绑定）
- Unix 套接字权限仅 macOS 支持
- 本地/私有地址即使在允许列表中也不被通配符匹配允许，必须精确匹配
