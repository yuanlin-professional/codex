# `token.rs` -- Windows 安全令牌操作

## 功能概述

本模块封装了 Windows 安全令牌的底层操作，是 Windows 沙箱的核心安全机制。它通过 `CreateRestrictedToken` API 创建受限令牌，移除大部分权限（`DISABLE_MAX_PRIVILEGE | LUA_TOKEN | WRITE_RESTRICTED`），并将 Capability SID、Logon SID 和 Everyone SID 添加为受限组。还负责设置默认 DACL 以允许沙箱进程创建管道/IPC 对象，以及从令牌中提取 Logon SID。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `world_sid` | `pub unsafe fn world_sid() -> Result<Vec<u8>>` | 创建 Everyone (World) SID |
| `convert_string_sid_to_sid` | `pub unsafe fn convert_string_sid_to_sid(s: &str) -> Option<*mut c_void>` | 将字符串 SID 转换为二进制 SID |
| `get_current_token_for_restriction` | `pub unsafe fn get_current_token_for_restriction() -> Result<HANDLE>` | 获取当前进程令牌用于后续限制 |
| `get_logon_sid_bytes` | `pub unsafe fn get_logon_sid_bytes(h_token) -> Result<Vec<u8>>` | 从令牌中提取 Logon SID（含 linked token 回退） |
| `create_readonly_token_with_cap` | `pub unsafe fn create_readonly_token_with_cap(psid) -> Result<(HANDLE, *mut c_void)>` | 创建带单个 Capability SID 的只读受限令牌 |
| `create_workspace_write_token_with_caps_from` | `pub unsafe fn create_workspace_write_token_with_caps_from(base, psids) -> Result<HANDLE>` | 创建带多个 Capability SID 的工作区写入受限令牌 |
| `set_default_dacl` | `unsafe fn set_default_dacl(h_token, sids) -> Result<()>` | 设置宽松的默认 DACL 允许 PowerShell 管道工作 |

## 依赖关系

- **引用的 crate**: `windows_sys`, `anyhow`, 内部 `winutil`
- **被引用方**: `lib.rs`（windows_impl 模块）、`setup_orchestrator.rs`、`audit.rs`

## 实现备注

- 受限令牌使用 `DISABLE_MAX_PRIVILEGE | LUA_TOKEN | WRITE_RESTRICTED` 标志，这是 Windows 上最严格的令牌限制组合
- Logon SID 提取含回退逻辑：先查当前令牌，失败则尝试 linked token（处理 UAC 场景）
- 默认 DACL 包含 Logon SID、Everyone 和所有 Capability SID，以 GENERIC_ALL 权限授予，确保沙箱进程创建的对象可被自身访问
- `SeChangeNotifyPrivilege` 被显式启用以允许目录遍历
