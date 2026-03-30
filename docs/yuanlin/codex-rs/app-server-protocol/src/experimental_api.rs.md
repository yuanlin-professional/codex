# `experimental_api.rs` — 实验性 API 标记框架

## 功能概述

定义了 Codex app-server 协议中实验性 API 的标记和检查框架。通过 `ExperimentalApi` trait 和 `ExperimentalField` 描述符，允许协议类型声明特定变体或字段为实验性的，并在运行时检查某个请求/通知是否使用了实验性功能。结合 `inventory` crate 实现了编译时注册、运行时发现的实验性字段清单。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ExperimentalApi` | trait | 判断协议类型是否使用了实验性功能 |
| `ExperimentalField` | 结构体 | 实验性字段描述（类型名、字段名、原因标识） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `experimental_fields` | `fn experimental_fields() -> Vec<&'static ExperimentalField>` | 返回所有注册的实验性字段 |
| `experimental_required_message` | `fn experimental_required_message(reason: &str) -> String` | 生成实验性功能错误消息 |
| `ExperimentalApi::experimental_reason` | `fn experimental_reason(&self) -> Option<&'static str>` | 返回实验性原因标识 |

## 依赖关系

- **引用的 crate**: `inventory`, `std::collections`
- **被引用方**: `common.rs` 中的宏生成代码，`codex_experimental_api_macros` 派生宏

## 实现备注

- 为 `Option<T>`、`Vec<T>`、`HashMap<K,V>`、`BTreeMap<K,V>` 提供了 blanket 实现，支持嵌套实验性检查。
- 测试使用 `#[derive(ExperimentalApi)]` 派生宏验证各种枚举/结构体形状（单元变体、元组变体、命名字段变体、嵌套字段、集合、Map）的实验性标记传播。
