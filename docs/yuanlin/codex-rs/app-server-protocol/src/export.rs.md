# `export.rs` — TypeScript 与 JSON Schema 生成引擎

## 功能概述

实现了将 app-server 协议类型导出为 TypeScript 类型定义和 JSON Schema 的完整生成流水线。负责遍历所有客户端请求/通知、服务端请求/通知类型及其关联的参数和响应类型，生成对应的 `.ts` 文件和 `.json` Schema 文件。支持实验性 API 过滤、Prettier 格式化、索引文件生成和内部 Schema 合并等功能。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `GenerateTsOptions` | 结构体 | TypeScript 生成选项（是否包含实验性 API） |
| `GeneratedSchema` | 结构体 | 已生成的 Schema 描述（名称 + 文件路径） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `generate_ts` | `fn generate_ts(out_dir, prettier) -> Result<()>` | 生成 TypeScript 类型文件 |
| `generate_ts_with_options` | `fn generate_ts_with_options(out_dir, prettier, options) -> Result<()>` | 带选项生成 TypeScript |
| `generate_json` | `fn generate_json(out_dir) -> Result<()>` | 生成 JSON Schema |
| `generate_json_with_experimental` | `fn generate_json_with_experimental(out_dir, experimental) -> Result<()>` | 带实验性标记生成 JSON Schema |
| `generate_types` | `fn generate_types(out_dir) -> Result<()>` | 仅生成 ts-rs 类型文件 |
| `generate_internal_json_schema` | `fn generate_internal_json_schema() -> Value` | 生成内部 JSON Schema（用于 Codex Cloud） |

## 依赖关系

- **引用的 crate**: `anyhow`, `schemars`, `serde_json`, `ts_rs`, `codex_protocol`
- **被引用方**: `bin/export.rs`、`schema_fixtures.rs`

## 实现备注

- 使用特殊定义列表（`SPECIAL_DEFINITIONS`、`FLAT_V2_SHARED_DEFINITIONS`）控制哪些类型需要扁平化的 v2 共享 Schema。
- 实验性 API 过滤通过扫描 TypeScript AST 中的类型名匹配来移除非实验性构建中的实验性定义。
- 生成的 TypeScript 文件头部带有 `GENERATED_TS_HEADER` 标识。
