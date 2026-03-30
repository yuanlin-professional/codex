# `index.ts` -- SDK 公共导出入口

## 功能概述

`index.ts` 是 Codex TypeScript SDK 的公共 API 入口文件。它重新导出所有面向用户的类型和类，包括事件类型、条目类型、Thread 类、Codex 类以及各种配置选项类型。

## 关键类型/接口

| 名称 | 类型 | 说明 |
|------|------|------|
| `ThreadEvent` 等 | re-export | 从 `./events` 导出的所有事件类型 |
| `ThreadItem` 等 | re-export | 从 `./items` 导出的所有条目类型 |
| `Thread` | re-export | 从 `./thread` 导出的线程类及相关类型 |
| `Codex` | re-export | 从 `./codex` 导出的主类 |
| `CodexOptions` | re-export | 从 `./codexOptions` 导出的配置类型 |
| `ThreadOptions` 等 | re-export | 从 `./threadOptions` 导出的线程选项类型 |
| `TurnOptions` | re-export | 从 `./turnOptions` 导出的回合选项类型 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| 无 | -- | 此文件仅包含 re-export 语句 |

## 依赖关系

- **引用的模块**: `./events`、`./items`、`./thread`、`./codex`、`./codexOptions`、`./threadOptions`、`./turnOptions`
- **被引用方**: 所有外部使用者通过 `@openai/codex-sdk` 引入时的入口、`tests/runStreamed.test.ts`

## 实现备注

- 该文件是 `tsup.config.ts` 中配置的唯一构建入口 `entry: ["src/index.ts"]`。
- 使用 `export type` 语法确保仅类型的导出在编译后不产生运行时代码。
