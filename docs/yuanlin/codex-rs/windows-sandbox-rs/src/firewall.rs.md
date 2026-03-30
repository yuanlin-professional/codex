# `firewall.rs` -- Windows 防火墙规则管理

## 功能概述

本模块通过 Windows Firewall COM API（`INetFwPolicy2`/`INetFwRule3`）管理沙箱的出站防火墙规则。它为离线沙箱用户创建出站阻止规则（阻止非回环地址的所有 IP 协议），并通过代理端口白名单精细控制回环 TCP/UDP 访问。支持代理端口变更时的幂等更新，以及从代理模式到本地绑定模式的切换。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ensure_offline_outbound_block` | `pub fn ensure_offline_outbound_block(offline_sid, log) -> Result<()>` | 创建/更新阻止所有非回环出站流量的防火墙规则 |
| `ensure_offline_proxy_allowlist` | `pub fn ensure_offline_proxy_allowlist(offline_sid, proxy_ports, allow_local_binding, log) -> Result<()>` | 管理回环代理端口白名单规则 |
| `ensure_block_rule` | `fn ensure_block_rule(rules, spec, log) -> Result<()>` | 创建或更新单条阻止规则（幂等操作） |
| `configure_rule` | `fn configure_rule(rule, spec) -> Result<()>` | 配置防火墙规则的所有属性（方向、动作、协议、地址等） |
| `blocked_loopback_tcp_remote_ports` | `fn blocked_loopback_tcp_remote_ports(proxy_ports) -> Option<String>` | 计算回环 TCP 阻止端口范围（排除允许的代理端口） |

## 依赖关系

- **引用的 crate**: `windows` (COM API), `anyhow`, `chrono`, `codex_windows_sandbox`
- **被引用方**: 由提权设置辅助程序调用

## 实现备注

- 使用 COM 接口（`CoCreateInstance` + `INetFwPolicy2`）操作 Windows 防火墙
- 规则使用 `LocalUserAuthorizedList` SDDL 限定仅对特定 SID 生效
- 代理端口白名单通过计算补集端口范围实现：例如允许端口 3128 则阻止 1-3127 和 3129-65535
- 包含 read-back 验证，确保规则 SID 范围与预期一致
- `allow_local_binding` 模式下移除所有回环阻止规则，允许沙箱进程绑定本地端口
- 规则名称使用稳定标识符（如 `codex_sandbox_offline_block_outbound`），支持跨安装幂等更新
