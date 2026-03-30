# `schema_loader.rs` -- 生成的 Hook JSON Schema 加载器

## 功能概述

本文件使用 `OnceLock` 实现 Hook 输入/输出 JSON Schema 的懒加载。它通过 `include_str!` 在编译时嵌入预生成的 JSON Schema 文件，运行时按需解析为 `serde_json::Value`。每种事件类型（pre_tool_use、post_tool_use、session_start、user_prompt_submit、stop）都有对应的 input 和 output schema。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `GeneratedHookSchemas` | struct | 包含 10 个 `serde_json::Value` 字段，分别对应 5 种事件的 input/output schema |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `generated_hook_schemas` | `fn() -> &'static GeneratedHookSchemas` | 返回全局唯一的 schema 实例（OnceLock 懒初始化） |
| `parse_json_schema` | `fn(name, schema_str) -> Value` | 解析 JSON Schema 字符串，失败时 panic |

## 依赖关系

- **引用的 crate**: `serde_json`
- **编译时依赖**: `schema/generated/` 目录下的 10 个 `.schema.json` 文件
- **被引用方**: `engine::mod.rs` 中 `ClaudeHooksEngine::new` 调用以预加载 schema

## 实现备注

- 使用 `#[allow(dead_code)]` 标注 `GeneratedHookSchemas`，表明 schema 主要用于验证目的，字段可能未被直接读取。
- 内嵌测试验证所有 10 个 schema 都能成功解析且 type 字段为 "object"。
