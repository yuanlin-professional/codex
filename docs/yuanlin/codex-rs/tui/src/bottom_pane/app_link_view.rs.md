# `app_link_view.rs` -- App Link 应用链接视图

## 功能概述
该文件实现了 TUI 中的应用链接视图（AppLinkView），用于展示和管理 ChatGPT 应用的安装、启用和管理操作。视图支持两个屏幕：链接主屏幕和安装确认屏幕，用户可以通过键盘导航选择操作，包括在浏览器中打开 ChatGPT 链接、启用/禁用应用等。当视图作为工具建议（tool suggestion）使用时，还会通过 elicitation 机制向 MCP 服务器发送接受或拒绝决策。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `AppLinkScreen` | 枚举 | 视图所处的屏幕状态：`Link`（链接页面）或 `InstallConfirmation`（安装确认页面） |
| `AppLinkSuggestionType` | 枚举 | 建议类型：`Install`（安装）或 `Enable`（启用） |
| `AppLinkElicitationTarget` | 结构体 | MCP elicitation 目标信息，包含 thread_id、server_name 和 request_id |
| `AppLinkViewParams` | 结构体 | 创建视图所需的参数集合，包含应用 ID、标题、描述、URL 等 |
| `AppLinkView` | 结构体 | 主视图结构体，持有应用信息、UI 状态和事件发送器 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn new(params: AppLinkViewParams, app_event_tx: AppEventSender) -> Self` | 从参数构造视图实例 |
| `action_labels` | `fn action_labels(&self) -> Vec<&'static str>` | 根据当前屏幕和安装状态返回可选操作标签 |
| `activate_selected_action` | `fn activate_selected_action(&mut self)` | 执行当前选中的操作，根据是否为工具建议走不同分支 |
| `handle_key_event` | `fn handle_key_event(&mut self, key_event: KeyEvent)` | 处理键盘输入，支持方向键/Tab/数字键/Enter/Esc |
| `render` | `fn render(&self, area: Rect, buf: &mut Buffer)` | 渲染视图内容，包含内容区、操作列表和提示行 |

## 依赖关系
- **引用的 crate**: `codex_protocol`（ThreadId, ElicitationAction, McpRequestId）、`crossterm`、`ratatui`、`textwrap`
- **被引用方**: `mod.rs` 通过 `pub(crate) use` 导出 `AppLinkView`、`AppLinkViewParams`、`AppLinkSuggestionType`、`AppLinkElicitationTarget`

## 实现备注
- 视图实现了 `BottomPaneView` trait（处理键盘事件和取消）和 `Renderable` trait（计算高度和渲染）
- 对于工具建议模式，操作逻辑根据 `suggestion_type`（Install/Enable）分别处理
- 安装流程：点击安装后打开浏览器，然后切换到确认屏幕等待用户确认已安装
- 文件包含大量测试用例，覆盖了安装/启用/拒绝场景及快照测试
