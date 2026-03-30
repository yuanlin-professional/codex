# `codex.ts` -- Codex SDK 主入口类

## 功能概述

`codex.ts` 定义了 `Codex` 类，是 Codex TypeScript SDK 的顶层入口。用户通过实例化该类并调用 `startThread()` 或 `resumeThread()` 方法来与 Codex 代理进行对话。该类内部持有 `CodexExec` 实例和配置选项，负责将底层执行器与线程管理串联起来。

## 关键类型/接口

| 名称 | 类型 | 说明 |
|------|------|------|
| `Codex` | class | SDK 主类，用于创建和恢复对话线程 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `constructor` | `(options?: CodexOptions)` | 初始化 Codex 实例，解析配置并创建 `CodexExec` 执行器 |
| `startThread` | `(options?: ThreadOptions): Thread` | 创建新的对话线程 |
| `resumeThread` | `(id: string, options?: ThreadOptions): Thread` | 根据线程 ID 恢复已有对话（会话持久化在 `~/.codex/sessions`） |

## 依赖关系

- **引用的模块**: `./codexOptions`（CodexOptions 类型）、`./exec`（CodexExec 类）、`./thread`（Thread 类）、`./threadOptions`（ThreadOptions 类型）
- **被引用方**: `./index.ts`（公共导出）、`tests/testCodex.ts`（测试辅助）、`samples/`（示例代码）

## 实现备注

- 该类非常精简（约 40 行），主要职责是组装依赖并委托给 `Thread` 类。
- `CodexExec` 在构造函数中创建一次后被所有线程共享。
- `resumeThread` 通过将 `id` 传入 `Thread` 构造函数来实现会话恢复。
