# `lib.rs` — ExperimentalApi 过程宏

## 功能概述

提供 `#[derive(ExperimentalApi)]` 过程宏，自动为结构体和枚举生成实验性 API 检测逻辑。标记为 `#[experimental("reason")]` 的字段或变体在运行时可以通过 `experimental_reason()` 方法检测是否处于活跃状态（Option 非 None、Vec 非空、bool 为 true 等）。支持嵌套检测（`#[experimental(nested)]`）和序列化名称到 camelCase 的自动转换。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `derive_experimental_api` | proc_macro | 为 struct/enum 派生 `ExperimentalApi` trait 实现 |
| `derive_for_struct` | `(input, data) -> TokenStream` | 处理结构体：生成字段存在性检查、inventory 注册 |
| `derive_for_enum` | `(input, data) -> TokenStream` | 处理枚举：按变体生成 match arm |
| `experimental_presence_expr` | `(field, tuple_struct) -> Option<TokenStream>` | 根据字段类型生成"是否存在"的表达式 |
| `snake_to_camel` | `(s) -> String` | snake_case 到 camelCase 转换 |

## 依赖关系

- **引用的 crate**: `proc_macro`、`syn`、`quote`
- **被引用方**: 使用 `#[derive(ExperimentalApi)]` 的协议类型

## 实现备注

- 对 Option 类型检查 `is_some_and`，对 Vec/HashMap 检查 `!is_empty()`，对 bool 直接检查值。
- 通过 `inventory::submit!` 全局注册实验性字段信息。
