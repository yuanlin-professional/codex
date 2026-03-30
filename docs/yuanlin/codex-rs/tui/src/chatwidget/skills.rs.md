# `skills.rs` — 技能管理和工具提及解析

## 功能概述

本模块负责 TUI 中技能（Skills）的管理界面和工具提及（tool mentions）的文本解析。它提供了技能列表展示、启用/禁用切换、`$` 前缀的技能提及解析，以及从输入文本中提取 `@tool_name` 和 `[$tool_name](path)` 链接式提及的功能。同时处理 App（连接器）提及与技能提及之间的去重。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ToolMentions` | struct | 从文本中提取的工具提及集合，包含 names 和 linked_paths |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `open_skills_list` | `(&mut self)` | 插入 `$` 触发技能列表 |
| `open_manage_skills_popup` | `(&mut self)` | 打开技能启用/禁用管理弹窗 |
| `update_skill_enabled` | `(&mut self, path, enabled)` | 更新单个技能的启用状态 |
| `set_skills_from_response` | `(&mut self, response)` | 从服务器响应更新技能列表 |
| `collect_tool_mentions` | `(text, mention_paths) -> ToolMentions` | 从文本收集工具提及 |
| `find_skill_mentions_with_tool_mentions` | `(mentions, skills) -> Vec<SkillMetadata>` | 从工具提及中匹配技能 |
| `find_app_mentions` | `(mentions, apps, skill_names) -> Vec<AppInfo>` | 从工具提及中匹配 App 连接器 |

## 依赖关系

- **引用的 crate**: `codex_core::skills::model`, `codex_core::connectors`, `codex_chatgpt::connectors`
- **被引用方**: `ChatWidget` 技能和提及处理

## 实现备注

- 使用 `TOOL_MENTION_SIGIL`（`$`）作为提及前缀
- 链接式提及格式：`[$name](path)`，支持 skill:// 和 app:// 前缀
- 常见环境变量名（PATH、HOME 等）被过滤以避免误匹配
- 技能路径通过 `dunce::canonicalize` 标准化以处理跨平台路径差异
