# `process_id.rs` — 进程标识符类型

## 功能概述

定义 `ProcessId` 新类型（newtype），封装 `String` 并实现序列化、显示、哈希、比较等 trait。这是协议层面的逻辑进程标识符，不是操作系统 PID。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ProcessId` | struct | 透明序列化的字符串新类型，用于标识 exec-server 进程 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn(impl Into<String>) -> Self` | 从任何字符串类型构造 |
| `as_str` | `fn(&self) -> &str` | 获取底层字符串引用 |

## 依赖关系

- **引用的 crate**: `serde`
- **被引用方**: 几乎所有 exec-server 模块
