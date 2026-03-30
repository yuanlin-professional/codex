# `schema_fixtures.rs` — Schema Fixture 管理与验证

## 功能概述

提供了 app-server 协议 Schema fixture 文件的读取、生成、写入和比对功能。Schema fixture 是 TypeScript 类型定义和 JSON Schema 的快照文件，用于验证代码变更不会意外改变协议的 wire format。支持 fixture 文件的递归收集、JSON 规范化排序（使阵列/对象键顺序一致）、TypeScript 内容正规化（CRLF 转 LF、去除生成头）以及完整的 fixture 重新生成流程。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `SchemaFixtureOptions` | 结构体 | Fixture 生成选项（是否包含实验性 API） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `read_schema_fixture_tree` | `fn read_schema_fixture_tree(root) -> Result<BTreeMap<PathBuf, Vec<u8>>>` | 读取完整 fixture 树 |
| `read_schema_fixture_subtree` | `fn read_schema_fixture_subtree(root, label) -> Result<BTreeMap<PathBuf, Vec<u8>>>` | 读取 fixture 子树 |
| `write_schema_fixtures` | `fn write_schema_fixtures(root, prettier) -> Result<()>` | 重新生成所有 fixture |
| `write_schema_fixtures_with_options` | `fn write_schema_fixtures_with_options(root, prettier, options) -> Result<()>` | 带选项重新生成 fixture |
| `generate_typescript_schema_fixture_subtree_for_tests` | `fn ...() -> Result<BTreeMap<PathBuf, Vec<u8>>>` | 生成内存中的 TypeScript fixture 子树（用于测试） |

## 依赖关系

- **引用的 crate**: `anyhow`, `serde_json`, `ts_rs`
- **被引用方**: `lib.rs` 导出，`tests/schema_fixtures.rs` 和 `bin/write_schema_fixtures.rs` 使用

## 实现备注

- `canonicalize_json` 函数对 JSON 值进行深度排序：对象按键排序、数组在可导出排序键时按键排序（支持 `$ref`、`title` 和原始值类型），确保不同平台生成的 Schema 可比较。
- `read_file_bytes` 对 `.json` 文件做规范化重格式化，对 `.ts` 文件做 CRLF 转换和生成头去除，实现平台无关的 fixture 比较。
