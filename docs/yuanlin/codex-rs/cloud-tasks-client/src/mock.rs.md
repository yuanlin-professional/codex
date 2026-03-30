# `mock.rs` — 云任务 Mock 客户端实现

## 功能概述

实现 `CloudBackend` trait 的 Mock 版本，用于测试和开发。使用内存中的 `HashMap` 存储任务数据，支持所有 CloudBackend 操作的模拟响应。提供预设任务数据的 builder 方法。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `MockClient` | struct | 基于内存的 Mock CloudBackend 实现 |

## 依赖关系

- **被引用方**: `cloud-tasks-client/src/lib.rs`（mock feature）
