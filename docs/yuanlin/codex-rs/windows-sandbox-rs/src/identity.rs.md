# `identity.rs` -- 沙箱用户身份管理与凭据获取

## 功能概述

该文件负责管理 Windows 沙箱的用户身份选择与凭据获取。它从磁盘读取 setup marker 和加密的用户凭据文件，使用 DPAPI 解密密码，根据沙箱策略（离线/在线）选择合适的沙箱用户身份。当 setup 状态缺失或过期时，会触发提升权限的 setup 流程，并在每次命令执行前刷新 ACL 配置。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `SandboxIdentity` | struct | 内部身份信息，包含 username 和解密后的 password |
| `SandboxCreds` | struct | 公开的沙箱凭据，包含 username 和 password |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `require_logon_sandbox_creds` | `fn(policy, policy_cwd, command_cwd, env_map, codex_home, proxy_enforced) -> Result<SandboxCreds>` | 获取沙箱登录凭据的主入口，按需触发 setup 并刷新 ACL |
| `sandbox_setup_is_complete` | `fn(codex_home: &Path) -> bool` | 粗略检查沙箱 setup 工件是否存在且版本匹配 |
| `select_identity` | `fn(network_identity, codex_home) -> Result<Option<SandboxIdentity>>` | 根据网络身份类型选择对应的用户凭据并解密 |
| `load_marker` | `fn(codex_home: &Path) -> Result<Option<SetupMarker>>` | 读取并解析 setup marker 文件 |
| `load_users` | `fn(codex_home: &Path) -> Result<Option<SandboxUsersFile>>` | 读取并解析沙箱用户凭据文件 |
| `decode_password` | `fn(record: &SandboxUserRecord) -> Result<String>` | 将 base64+DPAPI 加密的密码解密为明文 |

## 依赖关系

- **引用的 crate**: `base64`, `anyhow`, `crate::dpapi`, `crate::logging`, `crate::setup`
- **被引用方**: `elevated_impl.rs` 中的 `run_windows_sandbox_capture`

## 实现备注

- 当 marker 版本不匹配或缺失时，通过 `run_elevated_setup` 触发带提升权限的 setup 流程
- 每次命令执行前都会调用 `run_setup_refresh` 刷新 ACL，确保当前目录的读写根正确
- 使用 `debug_log` 在 `SBX_DEBUG=1` 模式下输出诊断信息
