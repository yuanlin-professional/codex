# `acl.rs` -- Windows ACL（访问控制列表）操作模块

## 功能概述

该文件提供 Windows DACL（自主访问控制列表）的全面操作能力，包括获取路径或句柄的 DACL、检查特定 SID 是否具有读/写权限、添加允许/拒绝 ACE（访问控制条目）、撤销 ACE、以及授予 NUL 设备访问权限。这些函数是沙箱文件系统权限控制的基础设施，被用于实现读写策略的 ACL 配置。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `WRITE_ALLOW_MASK` | const | 写允许权限掩码（读+写+执行+删除+删除子项） |
| `CONTAINER_INHERIT_ACE` | const | ACE 容器继承标志 |
| `OBJECT_INHERIT_ACE` | const | ACE 对象继承标志 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `fetch_dacl_handle` | `unsafe fn(path: &Path) -> Result<(*mut ACL, *mut c_void)>` | 通过句柄获取路径的 DACL 和安全描述符 |
| `dacl_mask_allows` | `unsafe fn(p_dacl, psids, desired_mask, require_all_bits) -> bool` | 检查 DACL 中是否有匹配 SID 的允许 ACE 具有指定权限位 |
| `dacl_has_write_allow_for_sid` | `unsafe fn(p_dacl, psid) -> bool` | 检查 DACL 中是否有给定 SID 的写允许 ACE |
| `dacl_has_write_deny_for_sid` | `unsafe fn(p_dacl, psid) -> bool` | 检查 DACL 中是否有给定 SID 的写拒绝 ACE |
| `add_allow_ace` | `unsafe fn(path, psid) -> Result<bool>` | 添加读/写/执行允许 ACE |
| `add_deny_write_ace` | `unsafe fn(path, psid) -> Result<bool>` | 添加写/删除拒绝 ACE |
| `ensure_allow_write_aces` | `unsafe fn(path, sids) -> Result<bool>` | 确保多个 SID 都有写允许 ACE |
| `ensure_allow_mask_aces` | `unsafe fn(path, sids, allow_mask) -> Result<bool>` | 确保多个 SID 具有指定权限掩码的允许 ACE |
| `revoke_ace` | `unsafe fn(path, psid)` | 撤销路径上给定 SID 的所有 ACE |
| `allow_null_device` | `unsafe fn(psid)` | 授予 SID 对 NUL 设备的读写执行权限 |
| `path_mask_allows` | `fn(path, psids, desired_mask, require_all_bits) -> Result<bool>` | 基于路径的权限检查封装 |

## 依赖关系

- **引用的 crate**: `windows_sys`（Security、FileSystem API）, `anyhow`, `crate::winutil`
- **被引用方**: `workspace_acl.rs`, `elevated_impl.rs`, `command_runner_win.rs`, setup 模块等

## 实现备注

- 所有 DACL 操作函数均为 `unsafe`，要求调用者确保 SID 指针有效
- ACE 遍历时跳过 `INHERIT_ONLY_ACE` 标志的条目（仅影响子对象不影响当前对象）
- `allow_null_device` 通过打开 `\\\\.\\NUL` 并修改其内核对象 DACL 实现
- 使用 `MapGenericMask` 将通用权限位映射为文件特定权限位进行准确匹配
- `ensure_allow_mask_aces_with_inheritance` 在添加前先检查是否已有足够权限，避免不必要的 DACL 修改
