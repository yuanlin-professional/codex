# `proxy_routing.rs` -- Linux 沙箱代理路由桥接

## 功能概述

本模块实现了 Linux 沙箱中的托管代理路由功能。当沙箱运行在网络隔离模式（`--unshare-net`）但需要通过主机上的本地代理访问网络时，该模块在主机端创建 Unix Domain Socket (UDS) 桥接进程，将沙箱内部的 TCP 流量通过 UDS 转发到主机端的真实代理端点。它还负责在沙箱内部启动本地回环桥接、重写代理环境变量，以及清理过期的 socket 目录。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ProxyRouteSpec` | struct | 代理路由规范，包含一组路由条目（序列化传递给沙箱内部） |
| `ProxyRouteEntry` | struct | 单条路由条目：环境变量键 + UDS 路径 |
| `PlannedProxyRoute` | struct | 规划阶段的路由：环境变量键 + 目标端点地址 |
| `ProxyRoutePlan` | struct | 路由规划结果：路由列表 + 是否检测到代理配置 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `prepare_host_proxy_route_spec` | `pub fn prepare_host_proxy_route_spec() -> io::Result<String>` | 主机端入口：扫描代理环境变量，创建 UDS socket，启动桥接进程 |
| `activate_proxy_routes_in_netns` | `pub fn activate_proxy_routes_in_netns(spec: &str) -> io::Result<()>` | 沙箱内入口：启动本地 TCP-UDS 桥接，重写代理环境变量为本地回环端口 |
| `plan_proxy_routes` | `fn plan_proxy_routes(env: &HashMap<String, String>) -> ProxyRoutePlan` | 分析环境变量，提取可用的回环代理端点 |
| `spawn_host_bridge` | `fn spawn_host_bridge(endpoint, uds_path) -> io::Result<pid_t>` | fork 子进程作为主机端 UDS-TCP 双向桥接 |
| `spawn_local_bridge` | `fn spawn_local_bridge(uds_path) -> io::Result<u16>` | fork 子进程作为沙箱内 TCP-UDS 双向桥接 |
| `ensure_loopback_interface_up` | `fn ensure_loopback_interface_up() -> io::Result<()>` | 确保沙箱内的回环网络接口已启用（通过 ioctl） |
| `rewrite_proxy_env_value` | `fn rewrite_proxy_env_value(proxy_url, local_port) -> Option<String>` | 将代理 URL 重写为指向本地回环端口 |

## 依赖关系

- **引用的 crate**: `serde`, `serde_json`, `url`, `libc`
- **被引用方**: `linux_run_main.rs`

## 实现备注

- 使用 `fork()` 而非线程来创建桥接进程，确保桥接在 exec 后仍能独立运行
- 通过 `PR_SET_PDEATHSIG` 设置父进程死亡信号，防止桥接进程成为孤儿进程
- 使用 pipe 同步机制确保桥接进程就绪后再继续
- socket 目录使用 `0o700` 权限保护，防止其他进程访问
- 包含完整的单元测试模块，覆盖代理 URL 解析、路由规划、socket 目录清理等场景
