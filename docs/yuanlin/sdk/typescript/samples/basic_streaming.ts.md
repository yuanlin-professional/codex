# `basic_streaming.ts` -- 交互式流式对话示例

## 功能概述

`basic_streaming.ts` 是 Codex SDK 的交互式命令行流式对话示例。它创建 Codex 客户端和对话线程，通过 readline 接口持续读取用户输入，使用 `runStreamed()` 方法流式获取事件，并根据事件类型实时打印代理消息、推理过程、命令执行结果、文件变更、待办列表和 token 用量等信息。

## 关键类型/接口

| 名称 | 类型 | 说明 |
|------|------|------|
| 无自定义类型 | -- | 直接使用 SDK 导出的 `ThreadEvent` 和 `ThreadItem` 类型 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `handleItemCompleted` | `(item: ThreadItem): void` | 处理已完成条目：打印代理消息、推理摘要、命令执行结果和文件变更 |
| `handleItemUpdated` | `(item: ThreadItem): void` | 处理更新条目：打印待办列表的实时状态 |
| `handleEvent` | `(event: ThreadEvent): void` | 事件分发：根据事件类型调用对应处理函数 |
| `main` | `(): Promise<void>` | 主循环：读取输入、执行流式对话、输出结果 |

## 依赖关系

- **引用的模块**: `node:readline/promises`、`node:process`、`@openai/codex-sdk`（Codex、ThreadEvent、ThreadItem）、`./helpers.ts`（codexPathOverride）
- **被引用方**: 无（独立示例程序）

## 实现备注

- 使用 shebang `#!/usr/bin/env -S NODE_NO_WARNINGS=1 pnpm ts-node-esm --files` 可直接作为脚本运行。
- 主循环中跳过空输入，确保不向代理发送无意义请求。
- 展示了 SDK 所有主要条目类型的消费方式，是理解事件模型的最佳参考。
