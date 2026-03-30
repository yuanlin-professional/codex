# `local_tool.rs` -- 本地命令执行工具定义

## 功能概述
定义本地命令执行相关的工具集：`exec_command`（执行 shell 命令，支持 workdir、shell、tty、login_shell、timeout 和 exec_policy_hint 参数）、`shell`（简化版 shell 工具）、`write_stdin`（向 TTY 进程写入输入）和 `request_permissions`（请求执行权限）。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `CommandToolOptions` | struct | exec_command 工具选项：allow_login_shell、exec_permission_approvals_enabled |
| `ShellToolOptions` | struct | shell 工具选项：exec_permission_approvals_enabled |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `create_exec_command_tool` | `fn(options) -> ToolSpec` | 创建完整功能的命令执行工具 |
| `create_shell_tool` | `fn(options) -> ToolSpec` | 创建简化版 shell 工具 |
| `create_shell_command_tool` | `fn(options) -> ToolSpec` | 创建 shell_command 工具 |
| `create_write_stdin_tool` | `fn() -> ToolSpec` | 创建 TTY 输入写入工具 |
| `create_request_permissions_tool` | `fn() -> ToolSpec` | 创建权限请求工具 |

## 依赖关系
- **被引用方**: `lib.rs` 公开导出

## 实现备注
- exec_command 的 output_schema 包含 stdout、stderr 和 exit_code
- exec_policy_hint 参数仅在 exec_permission_approvals_enabled 时包含
