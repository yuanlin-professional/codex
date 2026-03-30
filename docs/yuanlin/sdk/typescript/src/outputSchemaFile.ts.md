# `outputSchemaFile.ts` -- 输出 Schema 临时文件管理

## 功能概述

`outputSchemaFile.ts` 提供了将 JSON Schema 对象写入临时文件的功能，供 Codex CLI 的 `--output-schema` 参数使用。它负责创建临时目录、写入 schema 文件，并返回清理函数以在使用完毕后删除临时文件。这是结构化输出（Structured Output）功能的基础设施。

## 关键类型/接口

| 名称 | 类型 | 说明 |
|------|------|------|
| `OutputSchemaFile` | type | 返回类型，包含可选的 `schemaPath` 和 `cleanup` 清理函数 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `createOutputSchemaFile` | `(schema: unknown): Promise<OutputSchemaFile>` | 将 schema 写入临时文件，返回路径和清理函数 |
| `isJsonObject` | `(value: unknown): boolean` | 判断值是否为普通 JSON 对象（内部辅助函数） |

## 依赖关系

- **引用的模块**: `node:fs/promises`、`node:os`、`node:path`
- **被引用方**: `./thread.ts`（在 `runStreamedInternal` 中调用）

## 实现备注

- 当 `schema` 为 `undefined` 时返回空操作的 cleanup 函数，避免调用方进行额外判断。
- 临时目录创建在系统临时目录下，前缀为 `codex-output-schema-`。
- 写入失败时会先调用 cleanup 清理临时目录再抛出异常，防止资源泄漏。
