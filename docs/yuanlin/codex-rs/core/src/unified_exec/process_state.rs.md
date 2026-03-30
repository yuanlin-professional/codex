# `process_state.rs` — 进程状态数据结构

## 功能概述

定义了 `ProcessState` 结构体，用于在本地和远程进程之间共享退出状态和失败信息。提供了 `exited` 和 `failed` 两个状态转换方法，支持不可变的状态衍生模式。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ProcessState` | 结构体 | 进程共享状态，包含 `has_exited`（是否已退出）、`exit_code`（退出码）和 `failure_message`（失败消息） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `exited` | `fn(&self, exit_code: Option<i32>) -> Self` | 创建标记为已退出的新状态，保留已有的失败消息 |
| `failed` | `fn(&self, message: String) -> Self` | 创建标记为已退出且带失败消息的新状态，保留已有的退出码 |

## 依赖关系

- **引用的 crate**: 无外部依赖
- **被引用方**: `process.rs`（通过 `watch` 通道管理进程状态）
