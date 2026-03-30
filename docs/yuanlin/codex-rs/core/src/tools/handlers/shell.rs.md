# `shell.rs` — Shell 命令执行工具处理器

## 功能概述
该文件实现了两个 shell 命令执行工具的处理器：`ShellHandler`（通用 shell 工具，接受命令数组或本地 shell payload）和 `ShellCommandHandler`（自由形式 shell 命令工具，支持 Classic 和 ZshFork 两种后端）。它们共享核心执行逻辑 `run_exec_like`，负责权限管理、沙箱策略检查、apply_patch 拦截、命令审批流程以及通过 `ShellRuntime` 编排实际执行。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ShellHandler` | struct | 通用 shell 工具处理器，支持 `Function` 和 `LocalShell` 两种 payload |
| `ShellCommandHandler` | struct | 自由形式 shell 命令处理器，包含 `backend` 字段 |
| `ShellCommandBackend` | enum (私有) | 后端类型：`Classic` 或 `ZshFork` |
| `RunExecLikeArgs` | struct (私有) | `run_exec_like` 方法的参数集合 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ShellHandler::to_exec_params` | `fn to_exec_params(params, turn_context, thread_id) -> ExecParams` | 从 `ShellToolCallParams` 构建 `ExecParams` |
| `ShellCommandHandler::to_exec_params` | `fn to_exec_params(params, session, turn_context, thread_id, allow_login_shell) -> Result<ExecParams, FunctionCallError>` | 从 `ShellCommandToolCallParams` 构建 `ExecParams`，使用用户 shell 派生命令参数 |
| `ShellCommandHandler::resolve_use_login_shell` | `fn resolve_use_login_shell(login, allow_login_shell) -> Result<bool, FunctionCallError>` | 解析登录 shell 配置，在不允许时拒绝显式请求 |
| `ShellHandler::run_exec_like` | `async fn run_exec_like(args: RunExecLikeArgs) -> Result<FunctionToolOutput, FunctionCallError>` | 核心执行逻辑：合并环境变量、处理权限提升、拦截 apply_patch、创建审批需求、通过 ShellRuntime 执行命令 |
| `shell_payload_command` | `fn shell_payload_command(payload) -> Option<String>` | 从 payload 中提取用于显示的命令字符串 |
| `is_mutating` | `async fn is_mutating(&self, invocation) -> bool` | 判断命令是否为变更操作（非安全命令） |
| `pre_tool_use_payload` / `post_tool_use_payload` | 多个实现 | 生成工具使用前/后的事件 payload |

## 依赖关系
- **引用的 crate**: `async_trait`, `codex_protocol`, `codex_features`, `codex_shell_command`, `serde_json`
- **内部依赖**: `exec`, `exec_env`, `exec_policy`, `is_safe_command`, `shell`, `tools::context`, `tools::events`, `tools::handlers::apply_patch`, `tools::orchestrator`, `tools::runtimes::shell`, `tools::sandboxing`
- **被引用方**: 工具注册表中注册为 `shell` 和 `shell_command` 工具

## 实现备注
- `run_exec_like` 是两个处理器共享的核心方法，实现了完整的命令执行管道
- 支持通过 `intercept_apply_patch` 拦截 apply_patch 命令并走专用路径
- 权限管理涉及多层检查：粘性回合权限、附加权限规范化、沙箱覆盖策略、审批策略守卫
- 依赖环境变量通过 `session.dependency_env()` 获取并合并到执行环境
- `ShellCommandHandler` 通过 `From<ShellCommandBackendConfig>` 支持从配置创建
- 非 `OnRequest` 审批策略下，禁止请求沙箱权限提升
