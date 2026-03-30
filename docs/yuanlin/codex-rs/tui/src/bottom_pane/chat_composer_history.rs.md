# `chat_composer_history.rs` -- 聊天编辑器历史管理

## 功能概述
该文件实现了聊天编辑器的 Shell 风格历史导航功能（Up/Down 键浏览历史）。`ChatComposerHistory` 管理本地会话历史和持久化跨会话历史的按需加载，支持异步获取远程历史条目。历史条目包含文本、文本元素、图片路径、远程图片 URL、提及绑定和待处理粘贴等完整的草稿状态。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `HistoryEntry` | 结构体 | 历史条目，包含文本和完整的草稿元数据（图片、提及、粘贴等） |
| `ChatComposerHistory` | 结构体 | 历史管理器，持有本地历史、已获取的远程历史和导航游标 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `navigate_up` | `fn navigate_up(&mut self, tx) -> Option<HistoryEntry>` | 向上浏览历史（更旧的条目） |
| `navigate_down` | `fn navigate_down(&mut self, tx) -> Option<HistoryEntry>` | 向下浏览历史（更新的条目） |
| `record_local_submission` | `fn record_local_submission(&mut self, entry)` | 记录用户提交的消息到本地历史 |
| `should_handle_navigation` | `fn should_handle_navigation(&self, text, cursor) -> bool` | 判断 Up/Down 是否应触发历史导航 |
| `on_entry_response` | `fn on_entry_response(&mut self, log_id, offset, entry) -> Option<HistoryEntry>` | 处理异步获取的历史条目响应 |

## 依赖关系
- **引用的 crate**: `codex_protocol`（Op, TextElement）、`std::collections::HashMap`
- **被引用方**: `ChatComposer` 持有 `ChatComposerHistory` 实例

## 实现备注
- 历史由两部分组成：持久化历史（按需异步获取）和本地会话历史（内存中）
- 去重机制：连续相同的提交不会重复记录
- 导航门控：非空文本时仅在光标位于行首或行尾且文本匹配上次回调条目时才允许导航
- 远程历史通过 `GetHistoryEntryRequest` Op 异步获取
