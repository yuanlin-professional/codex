# `mod.rs` -- 底部面板模块入口

## 功能概述
该文件是底部面板（BottomPane）模块的入口，定义了 `BottomPane` 结构体——聊天 UI 下半部分的核心容器。它持有 `ChatComposer`（输入编辑器）和一个视图栈（BottomPaneView 模态视图），负责输入路由、状态管理、视图推送/弹出以及渲染协调。同时管理状态指示器、统一执行页脚、待处理输入预览和待处理线程审批等辅助组件。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `BottomPane` | 结构体 | 底部面板主容器，持有编辑器、视图栈和各种状态 |
| `BottomPaneParams` | 结构体 | 创建 BottomPane 的参数集合 |
| `CancellationEvent` | 枚举 | 取消事件结果：Handled 或 NotHandled |
| `LocalImageAttachment` | 结构体 | 本地图片附件（占位符 + 路径） |
| `MentionBinding` | 结构体 | 提及绑定（提及文本 + 目标路径） |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn new(params: BottomPaneParams) -> Self` | 创建底部面板实例 |
| `handle_key_event` | `fn handle_key_event(&mut self, key_event) -> InputResult` | 路由键盘事件到活跃视图或编辑器 |
| `on_ctrl_c` | `fn on_ctrl_c(&mut self) -> CancellationEvent` | 处理 Ctrl+C，优先给活跃视图 |
| `push_approval_request` | `fn push_approval_request(&mut self, request, features)` | 推送审批请求模态 |
| `show_selection_view` | `fn show_selection_view(&mut self, params)` | 显示通用列表选择视图 |
| `set_task_running` | `fn set_task_running(&mut self, running: bool)` | 更新任务运行状态和状态指示器 |

## 依赖关系
- **引用的 crate**: `codex_core`、`codex_features`、`codex_file_search`、`codex_protocol`、`crossterm`、`ratatui`
- **被引用方**: `ChatWidget` 持有和操作 `BottomPane` 实例

## 实现备注
- 输入路由分层：BottomPane 决定视图还是编辑器接收按键，ChatWidget 决定中断/退出
- Esc 在任务运行时发送中断（Op::Interrupt），但弹出窗口或 `/agent` 命令编辑时不中断
- 双击退出快捷键目前已禁用（`DOUBLE_PRESS_QUIT_SHORTCUT_ENABLED = false`）
- 渲染使用 FlexRenderable 组合状态指示器、待处理预览和编辑器
