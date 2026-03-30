# `codexOptions.ts` -- Codex 实例配置选项定义

## 功能概述

`codexOptions.ts` 定义了创建 `Codex` 实例时可传入的配置选项类型。包括可执行文件路径覆盖、API 基础 URL、API 密钥、CLI 配置覆盖对象以及环境变量设置。这些选项会在底层通过 `CodexExec` 传递给 Codex CLI 进程。

## 关键类型/接口

| 名称 | 类型 | 说明 |
|------|------|------|
| `CodexConfigValue` | type alias | 配置值的递归类型，支持 `string`、`number`、`boolean`、数组和嵌套对象 |
| `CodexConfigObject` | type alias | 配置对象类型，键为字符串，值为 `CodexConfigValue` |
| `CodexOptions` | type alias | Codex 构造函数的选项类型 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| 无 | -- | 此文件仅包含类型定义，无运行时函数 |

## 依赖关系

- **引用的模块**: 无
- **被引用方**: `./codex.ts`、`./exec.ts`、`./thread.ts`、`./index.ts`、`tests/testCodex.ts`

## 实现备注

- `config` 字段接受 JSON 对象，SDK 会在 `exec.ts` 中将其扁平化为点分路径并序列化为 TOML 字面量，以兼容 CLI 的 `--config` 解析。
- `env` 字段提供时，SDK 不会从 `process.env` 继承环境变量，实现了完全隔离的环境控制。
