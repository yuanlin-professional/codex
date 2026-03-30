# `codexExecSpy.ts` -- Codex CLI spawn 调用监控工具

## 功能概述

`codexExecSpy.ts` 提供了 `codexExecSpy()` 函数，用于在测试中拦截和记录 `child_process.spawn` 的调用参数和环境变量。通过 Jest mock 机制包装 `spawn` 函数，保留原始行为的同时捕获每次调用的命令行参数和环境配置。返回的 `restore` 函数用于恢复原始实现。

## 关键类型/接口

| 名称 | 类型 | 说明 |
|------|------|------|
| 返回值 | `{ args, envs, restore }` | args 为参数数组的数组，envs 为环境变量数组，restore 恢复原始 mock |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `codexExecSpy` | `(): { args, envs, restore }` | 安装 spawn 间谍并返回记录引用和恢复函数 |

## 依赖关系

- **引用的模块**: `node:child_process`（通过 Jest mock）
- **被引用方**: `tests/run.test.ts`（验证 CLI 参数传递）

## 实现备注

- 使用 `jest.mock` 在模块级别替换 `child_process`，再通过 `mockImplementation` 包装实际的 `spawn`。
- 设计为可叠加使用：每次调用 `codexExecSpy()` 都保存前一个实现，`restore` 时恢复到前一层。
