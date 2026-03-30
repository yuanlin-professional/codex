# `agent_path.rs` — Agent 路径标识与验证

## 功能概述

定义了 `AgentPath` 类型，用于在 Codex 多 Agent 架构中唯一标识每个 Agent 在层级树中的位置。路径以 `/root` 为起点，支持绝对路径和相对路径的构建、解析及验证。该类型对路径段名施加了严格约束（仅允许小写字母、数字和下划线），并保留了 `root`、`.`、`..` 等特殊名称。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `AgentPath` | 结构体（newtype） | 对 `String` 的包装，表示一个经过验证的 Agent 层级路径 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `root` | `fn root() -> Self` | 创建根路径 `/root` |
| `from_string` | `fn from_string(path: String) -> Result<Self, String>` | 从字符串构建并验证绝对路径 |
| `is_root` | `fn is_root(&self) -> bool` | 判断是否为根节点 |
| `name` | `fn name(&self) -> &str` | 返回路径最后一段的名称 |
| `join` | `fn join(&self, agent_name: &str) -> Result<Self, String>` | 拼接子 Agent 名称构建子路径 |
| `resolve` | `fn resolve(&self, reference: &str) -> Result<Self, String>` | 解析相对引用或绝对引用为完整路径 |

## 依赖关系

- **引用的 crate**: `schemars`, `serde`, `ts_rs`, `std::fmt`, `std::ops::Deref`, `std::str::FromStr`
- **被引用方**: `protocol.rs` 中的 `AgentPath` re-export，以及各处 Agent 寻址逻辑

## 实现备注

- 实现了 `TryFrom<String>`、`TryFrom<&str>`、`FromStr`、`Display`、`Deref`、`AsRef<str>` 等特征，使其在 API 边界和字符串操作中非常灵活。
- 序列化/反序列化时先从 `String` 转换，并在 JSON Schema 中以 `String` 类型呈现。
- 内部辅助函数 `validate_agent_name`、`validate_absolute_path`、`validate_relative_reference` 确保路径格式严格正确。
- 包含全面的单元测试覆盖各种合法及非法路径场景。
