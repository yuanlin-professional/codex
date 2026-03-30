# `events.ts` -- 线程事件类型定义

## 功能概述

`events.ts` 定义了 Codex exec 输出的所有 JSONL 事件类型。这些事件类型对应 `codex-rs/exec/src/exec_events.rs` 中的 Rust 事件，是 SDK 流式处理的核心数据模型。事件涵盖线程生命周期（启动）、回合生命周期（开始/完成/失败）、条目生命周期（开始/更新/完成）以及错误事件。

## 关键类型/接口

| 名称 | 类型 | 说明 |
|------|------|------|
| `ThreadStartedEvent` | type | 线程启动事件，包含 `thread_id` |
| `TurnStartedEvent` | type | 回合开始事件，表示模型开始处理提示词 |
| `TurnCompletedEvent` | type | 回合完成事件，包含 `usage` 用量信息 |
| `TurnFailedEvent` | type | 回合失败事件，包含错误信息 |
| `ItemStartedEvent` | type | 条目开始事件，条目通常处于"进行中"状态 |
| `ItemUpdatedEvent` | type | 条目更新事件 |
| `ItemCompletedEvent` | type | 条目完成事件，条目到达终态 |
| `ThreadErrorEvent` | type | 不可恢复的流错误事件 |
| `Usage` | type | Token 用量描述，含输入/缓存输入/输出 token 数 |
| `ThreadError` | type | 致命错误，包含错误消息 |
| `ThreadEvent` | union type | 所有事件的联合类型 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| 无 | -- | 此文件仅包含类型定义，无运行时函数 |

## 依赖关系

- **引用的模块**: `./items`（ThreadItem 类型）
- **被引用方**: `./thread.ts`、`./index.ts`、`tests/runStreamed.test.ts`

## 实现备注

- 事件类型设计遵循 Rust 端 `exec_events.rs` 的结构，确保前后端事件模型一致。
- `ThreadEvent` 联合类型使用字面量 `type` 字段作为判别属性，便于 TypeScript 类型窄化（narrowing）。
