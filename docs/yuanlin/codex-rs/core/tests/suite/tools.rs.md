# `tools.rs` -- 测试模块

## 测试覆盖范围
全面测试 Codex 核心的工具执行集成，包括 shell 命令执行（exec_command/shell）、MCP 工具调用（echo/echo-tool）、MCP 资源操作、自定义工具（custom tool call）、工具搜索、工具重命名以及工具集配置。验证沙箱策略对工具执行的影响和审批流程。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `exec_command_tool_basic` | 基本 shell 命令执行和输出捕获 |
| `mcp_echo_tool_call` | MCP echo 工具调用和结构化结果 |
| `mcp_tool_with_dash_name` | 包含连字符的 MCP 工具名称处理 |
| `mcp_resource_operations` | MCP 资源列表和读取操作 |
| `custom_tool_call_integration` | 自定义工具调用集成测试 |
| `tool_approval_flow` | 工具执行审批流程 |
| `sandbox_policy_restricts_tools` | 沙箱策略对工具的限制 |
