# `environment_context.rs` — 环境上下文构建与序列化

## 功能概述

此文件定义了 `EnvironmentContext` 结构体，用于表示和传递 Codex 会话中的运行环境信息，包括当前工作目录、Shell 类型、日期、时区、网络策略和子代理信息。环境上下文可以从回合上下文（TurnContext）构建，支持差异比较（用于检测回合间的环境变化），并能序列化为 XML 格式注入到模型消息中。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `EnvironmentContext` | 结构体 | 完整的环境上下文，包含 cwd、shell、current_date、timezone、network、subagents |
| `NetworkContext` | 结构体 | 网络上下文，包含允许和拒绝的域名列表 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `EnvironmentContext::from_turn_context` | `(turn_context, shell) -> Self` | 从回合上下文构建环境上下文 |
| `EnvironmentContext::diff_from_turn_context_item` | `(before, after, shell) -> Self` | 计算两个回合上下文之间的差异 |
| `EnvironmentContext::equals_except_shell` | `(&self, other) -> bool` | 比较两个环境上下文（忽略 Shell） |
| `EnvironmentContext::serialize_to_xml` | `(self) -> String` | 将环境上下文序列化为 XML 标签格式 |
| `EnvironmentContext::with_subagents` | `(mut self, subagents) -> Self` | 附加子代理信息 |
| `impl From<EnvironmentContext> for ResponseItem` | — | 将环境上下文转换为 developer 消息用于注入模型输入 |

## 依赖关系

- **引用的 crate**: `codex_protocol`（`TurnContextItem`、`ResponseItem`）、`codex_config`、`crate::shell::Shell`、`crate::contextual_user_message`
- **被引用方**: 会话前缀构建和回合上下文注入使用此模块

## 实现备注

- XML 序列化是手动实现的（非使用 XML 库），因为 `quick-xml` 等库对枚举新类型的处理需要自定义宏
- Shell 信息只在初始环境上下文中包含，后续回合间比较时忽略
- 网络上下文从配置层栈的 requirements 中提取
- 差异计算中，如果 cwd 未变则不包含在差异中（输出 `None`）
- 子代理信息作为缩进文本嵌入 XML 标签中
