# `sandbox_users.rs` -- Windows 沙箱用户账户管理

## 功能概述

该文件负责在 Windows 上创建和管理沙箱执行所需的本地用户账户。它创建 `CodexSandboxUsers` 本地组，配置离线（offline）和在线（online）两个沙箱用户，通过 `NetUserAdd` / `NetUserSetInfo` Win32 API 管理用户，并使用 DPAPI 加密保存用户密码到磁盘。用户凭据以 JSON 格式写入 secrets 目录，同时写入 setup marker 文件记录配置版本信息。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `SandboxUserRecord` | struct | 序列化结构，包含 username 和 base64 编码的 DPAPI 加密密码 |
| `SandboxUsersFile` | struct | secrets 文件结构，包含版本号和 offline/online 用户记录 |
| `SetupMarker` | struct | setup marker 文件结构，包含版本、用户名、创建时间、代理端口等元数据 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `provision_sandbox_users` | `fn(codex_home, offline_username, online_username, proxy_ports, allow_local_binding, log) -> Result<()>` | 配置两个沙箱用户的主入口 |
| `ensure_sandbox_user` | `fn(username, password, log) -> Result<()>` | 确保用户存在并加入沙箱组 |
| `ensure_local_user` | `fn(name, password, log) -> Result<()>` | 通过 `NetUserAdd` 创建用户或通过 `NetUserSetInfo` 更新密码 |
| `ensure_local_group` | `fn(name, comment, log) -> Result<()>` | 通过 `NetLocalGroupAdd` 创建本地组 |
| `resolve_sid` | `fn(name: &str) -> Result<Vec<u8>>` | 通过 `LookupAccountNameW` 解析账户名到 SID 字节 |
| `write_secrets` | `fn(codex_home, ...) -> Result<()>` | 使用 DPAPI 加密密码并写入 JSON 凭据文件和 setup marker |
| `random_password` | `fn() -> String` | 生成 24 字符的随机密码 |

## 依赖关系

- **引用的 crate**: `windows_sys`（网络管理、安全 API）, `base64`, `rand`, `serde`, `chrono`, `codex_windows_sandbox`
- **被引用方**: 沙箱设置辅助程序（setup helper）

## 实现备注

- 仅在 `target_os = "windows"` 下编译
- 使用 `NetUserAdd` 创建用户时若已存在，回退到 `NetUserSetInfo` level 1003 更新密码
- 密码通过 DPAPI（`CRYPTPROTECT_LOCAL_MACHINE`）加密后 base64 编码存储
- 内置知名 SID 映射（Administrators、Users、Everyone 等）以优化解析
