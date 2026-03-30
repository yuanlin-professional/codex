# `request_permissions.rs` -- 测试模块

## 测试覆盖范围
全面测试 Codex 的权限请求和审批机制，包括命令执行审批（AskForApproval）、文件系统权限管理、细粒度审批配置（GranularApprovalConfig）、权限授予范围（session/run/permanent）以及沙箱策略与权限的交互。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `exec_approval_request` | 基本命令执行审批请求流程 |
| `auto_approve_when_always_approve` | AskForApproval::Never 时自动批准 |
| `granular_approval_blocks_unapproved` | 细粒度审批阻止未批准的命令 |
| `request_permissions_tool_grants_write` | request_permissions 工具授予写权限 |
| `permission_grant_scope_session` | 会话级权限授予 |
| `permission_grant_scope_permanent` | 永久权限授予 |
| `sandbox_policy_interaction` | 沙箱策略与权限系统的交互 |
