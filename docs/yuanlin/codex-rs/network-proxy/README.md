# network-proxy

## 功能概述

`codex-network-proxy` 是 Codex 项目的网络代理模块，提供 HTTP/HTTPS 和 SOCKS5 代理服务，并实现了基于域名的网络访问策略控制。它充当 Codex 沙箱环境与外部网络之间的中间层，支持 MITM（中间人）TLS 拦截以实现精细化的域名级别访问控制和审计。

## 架构说明

该 crate 由多个子系统协同工作：

1. **proxy (NetworkProxy / NetworkProxyBuilder)** - 代理服务器主体，支持 HTTP 正向代理和 SOCKS5 代理，可监听 TCP 端口或 Unix Domain Socket。
2. **http_proxy** - HTTP/HTTPS 代理实现。
3. **socks5** - SOCKS5 协议代理实现。
4. **mitm** - MITM TLS 拦截层，用于解密 HTTPS 流量以进行域名级别的策略检查。
5. **certs** - 动态证书生成，支持 MITM 场景下的 TLS 证书管理。
6. **policy / network_policy** - 网络访问策略引擎：
   - `NetworkPolicyDecider` - 策略决策器
   - `NetworkPolicyDecision` / `NetworkDecision` - 决策结果
   - `NetworkDecisionSource` - 决策来源
7. **config (NetworkProxyConfig)** - 代理配置，包括域名权限列表、网络模式和 Unix Socket 权限。
8. **state** - 代理状态管理和约束验证。
9. **runtime** - 运行时组件，包括被阻止请求的观察器和配置热重载。
10. **upstream** - 上游连接管理。

### 网络策略模型

```
请求 -> NetworkPolicyDecider -> NetworkPolicyDecision (Allow/Deny)
                                   |
                     NetworkDomainPermissions (域名白名单/黑名单)
                     NetworkUnixSocketPermissions (UDS 权限)
                     NetworkMode (全局模式)
```

## 目录结构

```
network-proxy/
  Cargo.toml
  src/
    lib.rs             # 模块入口，导出所有公共类型
    config.rs          # 网络代理配置（域名权限、网络模式等）
    proxy.rs           # 代理服务器主体（NetworkProxy、NetworkProxyBuilder）
    http_proxy.rs      # HTTP/HTTPS 代理
    socks5.rs          # SOCKS5 代理
    mitm.rs            # MITM TLS 拦截
    certs.rs           # 动态证书管理
    policy.rs          # 主机名规范化等策略工具
    network_policy.rs  # 网络策略决策引擎
    state.rs           # 代理状态与约束管理
    runtime.rs         # 运行时（被阻止请求观察器、配置重载）
    reasons.rs         # 阻止原因描述
    responses.rs       # 代理响应构建
    upstream.rs        # 上游连接
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `rama-*` (core, http, net, socks5, tcp, tls-rustls, unix) | 高性能代理框架 |
| `tokio` | 异步运行时 |
| `globset` | 域名通配符匹配 |
| `url` | URL 解析 |
| `serde` / `serde_json` | 配置序列化 |
| `codex-utils-absolute-path` / `codex-utils-home-dir` | 路径工具 |
| `codex-utils-rustls-provider` | TLS 提供者 |
| `clap` | 命令行参数解析 |
| `thiserror` | 错误类型定义 |

## 核心接口/API

### 代理服务器

- **`NetworkProxy`** - 代理服务器实例
- **`NetworkProxyBuilder`** - 代理构建器（Builder 模式）
- **`NetworkProxyHandle`** - 代理运行时句柄
- **`Args`** - 命令行参数定义

### 配置类型

- **`NetworkProxyConfig`** - 完整代理配置
- **`NetworkMode`** - 网络模式
- **`NetworkDomainPermissions`** / **`NetworkDomainPermission`** - 域名权限配置
- **`NetworkUnixSocketPermissions`** / **`NetworkUnixSocketPermission`** - Unix Socket 权限

### 策略引擎

- **`NetworkPolicyDecider`** - 策略决策器
- **`NetworkPolicyDecision`** / **`NetworkDecision`** - 决策结果
- **`NetworkPolicyRequest`** / **`NetworkPolicyRequestArgs`** - 策略请求
- **`NetworkProtocol`** - 网络协议枚举

### 运行时

- **`BlockedRequest`** / **`BlockedRequestObserver`** - 被阻止请求及其观察器
- **`ConfigReloader`** / **`ConfigState`** - 配置热重载
- **`NetworkProxyState`** - 代理运行时状态

### 工具函数

- `normalize_host(host: &str) -> String` - 主机名规范化
- `has_proxy_url_env_vars() -> bool` - 检查环境变量中是否设置了代理 URL
- `proxy_url_env_value() -> Option<String>` - 获取代理 URL 环境变量值
