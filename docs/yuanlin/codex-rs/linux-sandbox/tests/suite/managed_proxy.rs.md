# `managed_proxy.rs` — 测试模块

## 测试覆盖范围

Linux 沙箱在 managed proxy（受管代理）模式下的集成测试。测试仅在 Linux 平台运行，验证通过 bubblewrap (bwrap) 实现的网络隔离沙箱中代理环境变量的处理、网络流量路由和直接出站阻断、以及 AF_UNIX 套接字创建的权限控制。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `managed_proxy_mode_fails_closed_without_proxy_env` | 在 managed proxy 模式下，缺少代理环境变量（HTTP_PROXY 等）时，沙箱命令应失败并输出明确的错误消息 |
| `managed_proxy_mode_routes_through_bridge_and_blocks_direct_egress` | 验证 managed proxy 模式下：(1) HTTP 流量通过代理桥接正确路由；(2) 绕过代理的直接出站连接被阻断 |
| `managed_proxy_mode_denies_af_unix_creation_for_user_command` | 在 managed proxy 模式下，用户命令尝试创建 AF_UNIX 套接字应被 seccomp 策略拒绝（返回 PermissionError） |
