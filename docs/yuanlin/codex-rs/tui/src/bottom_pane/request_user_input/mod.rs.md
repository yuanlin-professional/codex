# `request_user_input/mod.rs` -- 用户输入请求叠加层

## 功能概述
该文件实现了用户输入请求叠加层（RequestUserInputOverlay），用于响应 Agent 的 `request_user_input` 事件。支持多问题导航（上一个/下一个）、自由文本输入和选项选择两种回答方式、答案编辑、提交前的未回答确认、以及 Tab 键在备注区域和选项间切换焦点。这是一个复杂的交互式表单组件，超过 2900 行代码。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `RequestUserInputOverlay` | 结构体 | 用户输入叠加层主结构，管理问题列表、答案、导航和编辑器状态 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn new(request, app_event_tx, ...) -> Self` | 从请求事件创建叠加层 |
| `handle_key_event` | `fn handle_key_event(&mut self, key_event)` | 处理键盘导航和输入 |
| `submit_answers` | `fn submit_answers(&mut self)` | 提交所有问题的答案 |
| `navigate_prev` / `navigate_next` | `fn navigate_prev/next(&mut self)` | 在问题间导航 |

## 依赖关系
- **引用的 crate**: `codex_protocol`（RequestUserInputEvent）、`crossterm`、`ratatui`
- **被引用方**: `mod.rs` 导出 `RequestUserInputOverlay`，由 `BottomPane` 使用

## 实现备注
- 支持秘密字段（密码输入），使用 `*` 掩码渲染
- 未回答问题提交时显示确认对话框
- 使用内嵌的 ChatComposer 处理自由文本回答
- 页脚显示导航提示、选项计数和问题进度
