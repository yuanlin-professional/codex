# `unified_exec.rs` -- 测试模块

## 测试覆盖范围
大型综合测试文件，覆盖 unified exec（统一执行）模式下 shell 命令的各类行为，包括基本执行、进程退出码、超时、环境变量（CODEX_SANDBOX）、shell 快照、命令拒绝、apply_patch 操作、进程清理、标准错误输出处理、MCP 工具禁用 tools 以及 apply_patch 的各种高级场景（shell 快照重试、自动 git add 等）。仅在非 Windows 系统上运行。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| 多个 exec_command 基本测试 | exec_command 执行 echo/cat 等命令并验证输出格式 |
| 超时测试 | exec_command 超时后进程被终止 |
| 环境变量测试 | 验证 CODEX_SANDBOX 和 CODEX_SANDBOX_NETWORK_DISABLED 环境变量设置 |
| shell_snapshot 测试 | 验证 shell 快照机制（提取上下文信息） |
| apply_patch 测试 | 验证 apply_patch 工具的文件创建、更新、删除及错误处理 |
| 进程清理测试 | turn 结束后后台进程被正确清理 |
| MCP 工具禁用测试 | disabled_tools 配置正确过滤 MCP 工具 |
