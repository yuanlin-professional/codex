# `chat_composer.rs` -- 聊天输入编辑器

## 功能概述
该文件实现了 TUI 中的聊天输入编辑器（ChatComposer），是用户与 Codex 交互的主要输入界面。它管理多行文本编辑、斜杠命令弹出、文件搜索弹出、技能提及弹出、图片附件、粘贴突发检测、输入历史导航、协作模式指示器、状态行和页脚等功能。这是整个底部面板中最复杂的组件，超过 8000 行代码。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ChatComposer` | 结构体 | 聊天编辑器主结构，持有文本区域、弹出状态、历史记录、附件等 |
| `ChatComposerConfig` | 结构体 | 编辑器配置参数 |
| `InputResult` | 枚举 | 输入处理结果：None、SubmitText、SlashCommand 等 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn new(...) -> Self` | 创建新的编辑器实例 |
| `handle_key_event` | `fn handle_key_event(&mut self, key_event) -> (InputResult, bool)` | 处理键盘输入并返回结果 |
| `handle_paste` | `fn handle_paste(&mut self, pasted: String) -> bool` | 处理粘贴文本 |
| `sync_popups` | `fn sync_popups(&mut self)` | 同步弹出窗口状态（命令、文件搜索、技能） |
| `current_text` | `fn current_text(&self) -> String` | 获取当前文本内容 |

## 依赖关系
- **引用的 crate**: `crossterm`、`ratatui`、`codex_protocol`、`codex_file_search`
- **被引用方**: `BottomPane`（mod.rs）直接持有和操作 ChatComposer

## 实现备注
- 支持三种弹出窗口：斜杠命令（/）、文件搜索（@）和技能/应用提及（$）
- 粘贴突发检测通过 `PasteBurst` 状态机实现，处理非括号粘贴终端
- 历史导航支持本地会话历史和持久化跨会话历史
- 页脚渲染包含复杂的宽度折叠逻辑，根据可用空间动态调整显示内容
