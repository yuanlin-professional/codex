# `procthreadattr.rs` — Windows 进程线程属性列表

## 功能概述

基于 WezTerm 源码（MIT 许可），封装 Windows `PROC_THREAD_ATTRIBUTE_LIST` 的创建和管理。`ProcThreadAttributeList` 提供 `with_capacity` 初始化和 `set_pty` 方法将 ConPTY 句柄关联到属性列表，用于在 `CreateProcessW` 中指定伪终端。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ProcThreadAttributeList` | struct | 进程线程属性列表封装 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `with_capacity` | `fn(num_attributes: DWORD) -> Result<Self>` | 创建指定属性数量的列表 |
| `set_pty` | `fn(&mut self, con: HPCON) -> Result<()>` | 设置 ConPTY 句柄属性 |

## 依赖关系

- **引用的 crate**: `winapi`, `anyhow`
- **被引用方**: `psuedocon.rs`

## 实现备注

- `Drop` 实现调用 `DeleteProcThreadAttributeList`。
