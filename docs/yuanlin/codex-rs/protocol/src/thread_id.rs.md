# `thread_id.rs` — 线程/会话唯一标识符

## 功能概述

定义了 `ThreadId` 类型，基于 UUID v7 生成的会话唯一标识符。UUID v7 包含时间戳信息，使得 ID 具有时间有序性。提供了完整的字符串互转、序列化/反序列化、Display、JsonSchema 等实现。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ThreadId` | 结构体 | 基于 UUID v7 的线程/会话唯一标识 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn new() -> Self` | 生成新的 UUID v7 标识 |
| `from_string` | `fn from_string(s: &str) -> Result<Self, uuid::Error>` | 从字符串解析 ThreadId |

## 依赖关系

- **引用的 crate**: `schemars`, `serde`, `ts_rs`, `uuid`
- **被引用方**: `lib.rs` 中 re-export；整个 Codex 系统中标识会话

## 实现备注

- 序列化为纯字符串格式（不是对象），JsonSchema 中类型为 `String`。
- `Default` 实现调用 `new()`，保证每次都生成唯一 ID（测试中验证不等于 nil UUID）。
