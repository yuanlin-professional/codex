# tools

## 功能概述

`codex-tools` 是 Codex 项目的工具定义与规格模块，集中管理所有可供 AI 代理调用的工具（Tools）定义。它负责将内部工具规格转换为 OpenAI Responses API 兼容格式，支持本地工具（shell 命令、文件操作）、MCP 工具、代理协作工具、代码模式工具、REPL 工具等多种类型。该模块独立于 `codex-core`，便于工具定义的复用和测试。

## 架构说明

该 crate 的核心职责是工具规格的定义和转换，按工具类型划分为以下子模块：

1. **local_tool** - 本地工具（Shell 命令、Exec 命令、文件写入、权限请求）
2. **agent_tool** - 代理协作工具（spawn/close/wait/resume/list/send_message 等多代理协作 API）
3. **agent_job_tool** - 代理任务工具（spawn_agents_on_csv、report_agent_job_result）
4. **mcp_tool** - MCP（Model Context Protocol）工具转换
5. **mcp_resource_tool** - MCP 资源操作工具（list/read resources）
6. **code_mode** - 代码模式工具（exec/wait 双工具）
7. **js_repl_tool** - JavaScript REPL 工具
8. **dynamic_tool** - 动态工具解析
9. **view_image** - 图片查看工具
10. **utility_tool** - 实用工具（list_dir、test_sync）
11. **request_user_input_tool** - 请求用户输入工具
12. **responses_api** - Responses API 格式转换层
13. **tool_definition** - 通用工具定义结构
14. **tool_spec** - 工具规格（ToolSpec）和配置
15. **json_schema** - JSON Schema 辅助工具

## 目录结构

```
tools/
  Cargo.toml
  src/
    lib.rs                      # 模块入口，统一导出
    local_tool.rs               # Shell/Exec 本地工具
    agent_tool.rs               # 多代理协作工具
    agent_job_tool.rs           # 代理任务工具
    mcp_tool.rs                 # MCP 工具
    mcp_resource_tool.rs        # MCP 资源工具
    code_mode.rs                # 代码模式工具
    js_repl_tool.rs             # JS REPL 工具
    dynamic_tool.rs             # 动态工具
    view_image.rs               # 图片查看工具
    utility_tool.rs             # 实用工具
    request_user_input_tool.rs  # 用户输入请求工具
    responses_api.rs            # Responses API 转换
    tool_definition.rs          # 工具定义结构
    tool_spec.rs                # 工具规格
    json_schema.rs              # JSON Schema 工具
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `codex-code-mode` | 代码模式工具定义 |
| `codex-protocol` | Codex 协议类型 |
| `rmcp` | MCP 协议（server 模式、schemars、macros） |
| `serde` / `serde_json` | 工具参数的序列化 |

## 核心接口/API

### 工具创建函数

**本地工具：**
- `create_shell_tool(options)` / `create_shell_command_tool(options)` - Shell 工具
- `create_exec_command_tool(options)` - Exec 命令工具
- `create_write_stdin_tool()` - 标准输入写入工具
- `create_request_permissions_tool()` - 权限请求工具

**代理协作工具：**
- `create_spawn_agent_tool_v1/v2(options)` - 创建子代理
- `create_close_agent_tool_v1/v2()` - 关闭代理
- `create_wait_agent_tool_v1/v2(options)` - 等待代理
- `create_list_agents_tool()` / `create_send_message_tool()` - 列出/发送消息

**代码模式工具：**
- `create_code_mode_tool()` / `create_wait_tool()` - 代码模式 exec/wait 工具

**MCP 工具：**
- `parse_mcp_tool()` - 解析 MCP 工具定义
- `mcp_tool_to_responses_api_tool()` - 转换为 Responses API 格式

### 类型定义

- **`ToolDefinition`** - 通用工具定义
- **`ToolSpec`** / **`ConfiguredToolSpec`** - 工具规格及其配置
- **`ResponsesApiTool`** / **`FreeformTool`** - Responses API 工具格式
- **`JsonSchema`** / **`AdditionalProperties`** - JSON Schema 辅助类型
