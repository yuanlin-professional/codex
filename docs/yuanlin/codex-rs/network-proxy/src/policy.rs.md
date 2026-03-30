# `policy.rs` -- 网络策略域名匹配引擎

## 功能概述
提供网络代理的域名策略匹配核心功能，包括主机名归一化、IP 地址公网/私网分类、域名通配符模式编译（使用 globset）以及域名约束比较。支持三种通配符模式：精确匹配（`example.com`）、子域匹配（`*.example.com`）和含顶域匹配（`**.example.com`）。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `Host` | struct | 归一化后的主机名字符串 |
| `DomainPattern` | enum | 域名模式：ApexAndSubdomains / SubdomainsOnly / Exact |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `normalize_host` | `fn(host) -> String` | 归一化主机名：小写化、去端口、去方括号、去尾部点 |
| `is_loopback_host` | `fn(host) -> bool` | 判断是否为回环地址（localhost/127.0.0.1/::1） |
| `is_non_public_ip` | `fn(ip) -> bool` | 判断 IP 是否为非公网地址（私网/回环/链路本地/CGNAT/保留） |
| `compile_allowlist_globset` | `fn(patterns) -> Result<GlobSet>` | 编译允许列表 glob 集合（允许全局通配符 `*`） |
| `compile_denylist_globset` | `fn(patterns) -> Result<GlobSet>` | 编译拒绝列表 glob 集合（禁止全局通配符 `*`） |
| `DomainPattern::allows` | `fn(&self, candidate) -> bool` | 判断此模式是否允许候选模式 |

## 依赖关系
- **引用的 crate**: `globset`, `url`
- **被引用方**: `runtime.rs`, `state.rs`, `mitm.rs`, `http_proxy.rs`, `socks5.rs`

## 实现备注
- `*.example.com` 展开为 `?*.example.com`（排除顶域本身）
- `**.example.com` 展开为 `example.com` 和 `?*.example.com`（包含顶域）
- IPv6 非公网判断包括 unique-local（fc00::/7）和 link-local（fe80::/10）
- IPv4 私网判断覆盖 CGNAT（100.64/10）、TEST-NET、Benchmarking 和 Reserved 范围
