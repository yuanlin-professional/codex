# `thread.ts` -- 对话线程管理

## 功能概述

`thread.ts` 定义了 `Thread` 类，代表与 Codex 代理的一次对话线程。线程支持多轮连续对话，提供同步运行（`run`）和流式运行（`runStreamed`）两种执行方式。`run` 方法收集所有事件后返回完整结果，`runStreamed` 方法以异步生成器方式逐个产出事件。该文件还包含输入规范化逻辑，支持纯文本和结构化输入（文本+图片）两种格式。

## 关键类型/接口

| 名称 | 类型 | 说明 |
|------|------|------|
| `Turn` / `RunResult` | type | 完整回合结果，包含 items 列表、finalResponse 和 usage |
| `StreamedTurn` / `RunStreamedResult` | type | 流式回合结果，包含事件的异步生成器 |
| `UserInput` | type | 用户输入的联合类型，支持 `text` 和 `local_image` |
| `Input` | type | 输入类型，可以是纯字符串或 `UserInput[]` 数组 |
| `Thread` | class | 对话线程类 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `Thread.constructor` | `(exec, options, threadOptions, id?)` | 内部构造函数（通过 Codex 类创建） |
| `Thread.id` | `get id(): string \| null` | 获取线程 ID，首次回合开始后才有值 |
| `Thread.runStreamed` | `(input, turnOptions?): Promise<StreamedTurn>` | 流式运行，返回事件异步生成器 |
| `Thread.run` | `(input, turnOptions?): Promise<Turn>` | 同步运行，等待回合完成后返回完整结果 |
| `normalizeInput` | `(input: Input): { prompt, images }` | 规范化输入，将结构化输入拆分为文本和图片 |

## 依赖关系

- **引用的模块**: `./codexOptions`、`./events`、`./exec`、`./items`、`./threadOptions`、`./turnOptions`、`./outputSchemaFile`
- **被引用方**: `./codex.ts`（startThread/resumeThread 创建 Thread）、`./index.ts`（公共导出）

## 实现备注

- `runStreamedInternal` 是核心实现，解析 JSONL 行为 `ThreadEvent` 对象并通过 `yield` 逐个产出。
- 线程 ID 在收到第一个 `thread.started` 事件时自动设置。
- `run` 方法内部复用 `runStreamedInternal`，收集 `item.completed` 事件中的条目和最终 `agent_message` 文本。
- 遇到 `turn.failed` 事件时会中断迭代并抛出错误。
- `outputSchema` 通过 `createOutputSchemaFile` 写入临时文件，在 `finally` 块中清理。
- 多个文本输入会用 `\n\n` 连接为单个提示词。
