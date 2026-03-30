# `request_user_input.rs` — 用户输入请求协议类型

## 功能概述

定义了 Agent 在计划模式(Plan mode)下主动向用户请求输入的协议类型。支持多问题问卷形式，每个问题可包含选项列表、是否为"其他"自由输入、是否为敏感信息等配置。用于 Agent 在执行前收集用户的澄清和决策。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `RequestUserInputQuestionOption` | 结构体 | 问题选项（标签 + 描述） |
| `RequestUserInputQuestion` | 结构体 | 单个问题（ID、标题、问题文本、选项列表、是否自由输入、是否敏感） |
| `RequestUserInputArgs` | 结构体 | 请求用户输入的工具参数（问题列表） |
| `RequestUserInputAnswer` | 结构体 | 单个问题的回答（答案字符串列表） |
| `RequestUserInputResponse` | 结构体 | 完整的用户输入响应（问题ID到回答的映射） |
| `RequestUserInputEvent` | 结构体 | 用户输入请求事件（call_id + turn_id + 问题列表） |

## 依赖关系

- **引用的 crate**: `schemars`, `serde`, `ts_rs`, `std::collections::HashMap`
- **被引用方**: `protocol.rs` 导出 `RequestUserInputEvent`

## 实现备注

- 使用 `#[serde(rename = "isOther")]` 和 `#[serde(rename = "isSecret")]` 以 camelCase 格式序列化布尔字段。
- 响应使用 `HashMap<String, RequestUserInputAnswer>` 将问题 ID 映射到答案。
