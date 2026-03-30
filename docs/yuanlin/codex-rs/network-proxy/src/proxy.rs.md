# `proxy.rs` -- 网络代理构建器与入口

## 功能概述
网络代理的顶层入口，提供 Builder 模式构建代理实例、管理 HTTP/SOCKS5 监听器的生命周期、生成子进程环境变量覆盖。支持 Codex 托管模式（自动分配回环端口）和非托管模式（使用配置端口），强制 Unix 套接字代理绑定到回环地址。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `NetworkProxy` | struct | 代理实例，包含状态、地址、策略决策器和保留的监听器 |
| `NetworkProxyBuilder` | struct | Builder 模式构建器 |
| `NetworkProxyHandle` | struct | 代理运行句柄，管理 HTTP 和 SOCKS5 任务 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `NetworkProxy::builder` | `fn() -> NetworkProxyBuilder` | 创建构建器 |
| `NetworkProxy::run` | `async fn(&self) -> Result<NetworkProxyHandle>` | 启动 HTTP 和 SOCKS5 代理监听 |
| `NetworkProxy::apply_to_env` | `fn(&self, env)` | 设置子进程代理环境变量 |
| `apply_proxy_env_overrides` | `fn(env, http_addr, socks_addr, ...)` | 覆盖 HTTP_PROXY/HTTPS_PROXY/ALL_PROXY 等 30+ 环境变量 |

## 依赖关系
- **引用的 crate**: `clap`, `tokio`, `anyhow`
- **被引用方**: codex core 启动网络代理

## 实现备注
- macOS 上 SOCKS5 启用时自动设置 `GIT_SSH_COMMAND` ProxyCommand（保留已有值）
- NO_PROXY 默认值覆盖 localhost/127.0.0.1/::1/\*.local 和常见私网 CIDR
- Windows 托管模式使用固定端口，端口忙碌时回退到临时端口
