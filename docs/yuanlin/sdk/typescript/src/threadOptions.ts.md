# `threadOptions.ts` -- 线程级配置选项

## 功能概述

`threadOptions.ts` 定义了创建对话线程时的配置选项类型及相关枚举。包括模型选择、沙箱模式、工作目录、推理能力等级、网络访问开关、Web 搜索模式和审批策略等配置。这些选项在每次回合执行时传递给 Codex CLI。

## 关键类型/接口

| 名称 | 类型 | 说明 |
|------|------|------|
| `ApprovalMode` | type | 审批模式：`never`、`on-request`、`on-failure`、`untrusted` |
| `SandboxMode` | type | 沙箱模式：`read-only`、`workspace-write`、`danger-full-access` |
| `ModelReasoningEffort` | type | 模型推理能力等级：`minimal`、`low`、`medium`、`high`、`xhigh` |
| `WebSearchMode` | type | Web 搜索模式：`disabled`、`cached`、`live` |
| `ThreadOptions` | type | 线程配置选项的完整类型 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| 无 | -- | 此文件仅包含类型定义 |

## 依赖关系

- **引用的模块**: 无
- **被引用方**: `./codex.ts`、`./exec.ts`、`./thread.ts`、`./index.ts`

## 实现备注

- `webSearchEnabled`（布尔值）是旧版 API，`webSearchMode`（三态枚举）是新版替代，两者在 `exec.ts` 中有优先级处理逻辑。
- `additionalDirectories` 允许代理访问工作目录之外的额外目录。
