# `read_acl_mutex.rs` -- Windows 沙箱读 ACL 互斥锁

## 功能概述

本模块提供了一个命名互斥锁（`Local\CodexSandboxReadAcl`）机制，用于协调多个 Codex 进程对 ACL 的并发访问。`ReadAclMutexGuard` 实现了 RAII 模式，在 drop 时自动释放互斥锁并关闭句柄。这确保了在同一台机器上运行多个 Codex 实例时，ACL 修改操作不会发生竞争条件。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ReadAclMutexGuard` | struct | RAII 互斥锁守卫，持有 Windows HANDLE |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `read_acl_mutex_exists` | `pub fn read_acl_mutex_exists() -> Result<bool>` | 检查互斥锁是否已被其他进程创建 |
| `acquire_read_acl_mutex` | `pub fn acquire_read_acl_mutex() -> Result<Option<ReadAclMutexGuard>>` | 尝试获取互斥锁，已存在则返回 None |

## 依赖关系

- **引用的 crate**: `windows_sys`, `anyhow`, 内部 `winutil::to_wide`
- **被引用方**: 内部 ACL 操作模块
