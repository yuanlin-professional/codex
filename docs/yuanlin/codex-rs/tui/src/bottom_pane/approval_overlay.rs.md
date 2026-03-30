# `approval_overlay.rs` -- 审批叠加层视图

## 功能概述
该文件实现了 TUI 中的审批叠加层（ApprovalOverlay），用于在 Agent 请求需要用户批准时显示模态对话框。支持四种审批请求类型：命令执行（Exec）、权限请求（Permissions）、补丁应用（ApplyPatch）和 MCP Elicitation。叠加层支持请求队列，当用户处理完当前请求后自动显示下一个排队的请求。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ApprovalRequest` | 枚举 | 四种审批请求类型：Exec、Permissions、ApplyPatch、McpElicitation |
| `ApprovalOverlay` | 结构体 | 审批叠加层主结构，包含当前请求、队列、选项列表和 ListSelectionView |
| `ApprovalDecision` | 枚举 | 审批决策：Review（常规审核决策）或 McpElicitation（MCP elicitation 操作） |
| `ApprovalOption` | 结构体 | 一个审批选项，包含标签、决策、快捷键 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn new(request, app_event_tx, features) -> Self` | 创建新的审批叠加层 |
| `enqueue_request` | `fn enqueue_request(&mut self, req)` | 将新请求加入队列 |
| `apply_selection` | `fn apply_selection(&mut self, idx)` | 执行用户选择的审批操作 |
| `handle_exec_decision` | `fn handle_exec_decision(...)` | 处理命令执行审批决策 |
| `handle_permissions_decision` | `fn handle_permissions_decision(...)` | 处理权限审批决策 |
| `exec_options` | `fn exec_options(...) -> Vec<ApprovalOption>` | 根据可用决策生成命令执行选项 |
| `build_header` | `fn build_header(request) -> Box<dyn Renderable>` | 为每种请求类型构建头部显示内容 |

## 依赖关系
- **引用的 crate**: `codex_protocol`（ReviewDecision、ElicitationAction、NetworkApprovalContext 等）、`codex_features`、`crossterm`、`ratatui`
- **被引用方**: `mod.rs` 导出 `ApprovalOverlay`、`ApprovalRequest`、`format_requested_permissions_rule`

## 实现备注
- 支持快捷键操作：y（批准）、a（会话级批准）、p（前缀级批准）、d（拒绝）、n（中止）、o（打开源线程）
- 网络审批场景有特殊处理：标题包含主机名，命令行不显示在头部，隐藏 execpolicy amendment 选项
- Ctrl+C 会中止当前请求并清空队列
- 支持跨线程审批，显示线程标签并提供 `o` 快捷键切换到源线程
