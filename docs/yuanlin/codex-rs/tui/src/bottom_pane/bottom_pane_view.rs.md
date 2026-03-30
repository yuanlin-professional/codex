# `bottom_pane_view.rs` -- 底部面板视图 trait 定义

## 功能概述
定义了 `BottomPaneView` trait，这是所有可以在底部面板中显示的视图必须实现的接口。该 trait 继承自 `Renderable`，提供了键盘事件处理、完成状态检查、取消处理、粘贴处理以及请求消费等统一接口。各种模态视图（如审批叠加层、选择列表、反馈视图等）都实现了此 trait。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `BottomPaneView` | trait | 底部面板中所有视图的统一接口 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `handle_key_event` | `fn handle_key_event(&mut self, _key_event: KeyEvent)` | 处理键盘事件，默认空实现 |
| `is_complete` | `fn is_complete(&self) -> bool` | 视图是否已完成，应被移除 |
| `on_ctrl_c` | `fn on_ctrl_c(&mut self) -> CancellationEvent` | 处理 Ctrl+C 取消操作 |
| `handle_paste` | `fn handle_paste(&mut self, _pasted: String) -> bool` | 处理粘贴事件 |
| `flush_paste_burst_if_due` | `fn flush_paste_burst_if_due(&mut self) -> bool` | 刷新待处理的粘贴突发状态 |
| `try_consume_approval_request` | `fn try_consume_approval_request(...)` | 尝试消费审批请求 |
| `try_consume_user_input_request` | `fn try_consume_user_input_request(...)` | 尝试消费用户输入请求 |
| `try_consume_mcp_server_elicitation_request` | `fn try_consume_mcp_server_elicitation_request(...)` | 尝试消费 MCP 服务器 elicitation 请求 |

## 依赖关系
- **引用的 crate**: `codex_protocol`（RequestUserInputEvent）、`crossterm`
- **被引用方**: 所有底部面板视图（ApprovalOverlay、ListSelectionView、FeedbackNoteView 等）均实现此 trait

## 实现备注
- 大多数方法提供合理的默认实现，子类型只需覆写所需的方法
- `prefer_esc_to_handle_key_event` 允许视图选择将 Esc 路由到 `handle_key_event` 而非 `on_ctrl_c`
- `view_id` 和 `selected_index` 支持外部刷新时保持选择状态
