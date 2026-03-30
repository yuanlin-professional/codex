# `workspace_acl.rs` -- 工作区目录 ACL 保护

## 功能概述

该文件提供工作区敏感子目录（`.codex` 和 `.agents`）的 ACL 写保护功能。通过在这些目录上添加写拒绝 ACE（Access Control Entry），防止沙箱用户修改工作区的配置和代理目录。同时提供辅助函数判断命令的 CWD 是否为工作区根目录。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| - | - | 本文件无公共结构体定义 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `is_command_cwd_root` | `fn(root: &Path, canonical_command_cwd: &Path) -> bool` | 判断规范化后的命令 CWD 是否等于工作区根目录 |
| `protect_workspace_codex_dir` | `unsafe fn(cwd: &Path, psid: *mut c_void) -> Result<bool>` | 对 `cwd/.codex` 添加写拒绝 ACE |
| `protect_workspace_agents_dir` | `unsafe fn(cwd: &Path, psid: *mut c_void) -> Result<bool>` | 对 `cwd/.agents` 添加写拒绝 ACE |
| `protect_workspace_subdir` | `unsafe fn(cwd, psid, subdir) -> Result<bool>` | 内部函数，对指定子目录添加写拒绝 ACE |

## 依赖关系

- **引用的 crate**: `crate::acl`（`add_deny_write_ace`）, `crate::path_normalization`
- **被引用方**: 沙箱策略初始化流程

## 实现备注

- 所有写保护函数均为 `unsafe`，因为需要传入有效的 SID 指针
- 仅在子目录实际存在（`is_dir()`）时才添加 ACE
