# `dynamic_tools.rs` — 动态工具定义与调用协议

## 功能概述

定义了 Codex 的动态工具系统协议类型，允许客户端在运行时注册自定义工具规格（工具名、描述、输入 Schema），并处理 Agent 发起的动态工具调用请求与响应。支持文本和图片两种输出类型，以及延迟加载（defer_loading）特性。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `DynamicToolSpec` | 结构体 | 动态工具规格定义（名称、描述、输入Schema、是否延迟加载） |
| `DynamicToolCallRequest` | 结构体 | Agent 发起的动态工具调用请求 |
| `DynamicToolResponse` | 结构体 | 动态工具调用的响应（内容项列表 + 成功标志） |
| `DynamicToolCallOutputContentItem` | 枚举 | 工具输出内容项（InputText / InputImage） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `DynamicToolSpec::deserialize` | 自定义反序列化 | 兼容旧版 `exposeToContext` 字段（取反映射为 `defer_loading`） |

## 依赖关系

- **引用的 crate**: `schemars`, `serde`, `serde_json`, `ts_rs`
- **被引用方**: `protocol.rs` 中的事件类型、`app-server-protocol` v2 中的工具调用逻辑

## 实现备注

- 自定义反序列化通过中间结构体 `DynamicToolSpecDe` 实现，将已弃用的 `exposeToContext` 字段取反后映射到 `defer_loading`。
- 输出内容使用 `#[serde(tag = "type")]` 标签化枚举，支持多类型混合返回。
