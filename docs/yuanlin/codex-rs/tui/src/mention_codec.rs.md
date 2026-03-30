# `mention_codec.rs` — 历史记录中 mention 链接的编解码

## 功能概述

处理 TUI 历史记录中工具 mention 的编码和解码。mention 在用户输入中显示为 `$name`，
在历史存储中编码为 `[$name](path)` 的 Markdown 链接格式。解码时还原为可见的
`$name` 文本并提取关联的路径信息。支持 `$` sigil（工具 mention）和
`@` sigil（插件 mention）两种格式。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `LinkedMention` | struct | mention 名称和关联路径 |
| `DecodedHistoryText` | struct | 解码后的文本和 mention 列表 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `encode_history_mentions` | `(text, mentions) -> String` | 将 `$name` 编码为 `[$name](path)` |
| `decode_history_mentions` | `(text) -> DecodedHistoryText` | 从 `[$name](path)` 还原为 `$name` |

## 依赖关系

- **引用的 crate**: `codex_core::mention_syntax`

## 实现备注

- 使用字节级扫描以处理 UTF-8 安全性，逐字节匹配 `[` 和 sigil 字符。
- `is_common_env_var` 过滤常见环境变量名（如 PATH、HOME）避免误匹配。
- `is_tool_path` 验证路径前缀（`app://`、`mcp://`、`plugin://`、`skill://`、`SKILL.md`）。
