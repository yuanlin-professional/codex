# tools/handlers/ -- 工具处理器

## 功能概述

`handlers/` 包含所有具体工具的处理器实现。每个处理器实现 `ToolHandler` trait，负责接收工具调用参数、执行具体操作并返回结果。涵盖 Shell 命令执行、补丁应用、MCP 工具调用、多代理管理、JavaScript REPL、工具搜索/建议、目录列表、图片查看、权限请求等全部工具类型。

## 目录结构

```
handlers/
├── mod.rs                   # 模块入口，导出所有处理器和辅助函数
├── shell.rs                 # ShellHandler / ShellCommandHandler：Shell 命令执行
├── apply_patch.rs           # ApplyPatchHandler：代码补丁应用（JSON 和自由文本两种模式）
├── mcp.rs                   # McpHandler：MCP 工具调用转发
├── mcp_resource.rs          # McpResourceHandler：MCP 资源操作
├── unified_exec.rs          # UnifiedExecHandler：统一执行进程工具
├── multi_agents.rs          # 多代理工具 v1（spawn/wait/send/close）
├── multi_agents_common.rs   # 多代理公共类型和常量
├── multi_agents_v2.rs       # 多代理工具 v2（assign_task/send_message/list_agents 等）
├── multi_agents/            # 多代理 v1 子模块
├── multi_agents_v2/         # 多代理 v2 子模块
├── agent_jobs.rs            # BatchJobHandler：批量代理任务
├── js_repl.rs               # JsReplHandler / JsReplResetHandler：JavaScript REPL
├── list_dir.rs              # ListDirHandler：目录列表
├── tool_search.rs           # ToolSearchHandler：工具搜索
├── tool_suggest.rs          # ToolSuggestHandler：工具建议（可发现插件/连接器）
├── plan.rs                  # PlanHandler：计划工具
├── view_image.rs            # ViewImageHandler：图片查看
├── request_permissions.rs   # RequestPermissionsHandler：权限请求
├── request_user_input.rs    # RequestUserInputHandler：用户输入请求
├── dynamic.rs               # DynamicToolHandler：动态工具处理
├── test_sync.rs             # TestSyncHandler：测试同步工具
└── tool_apply_patch.lark    # 补丁解析语法文件
```

## 依赖关系

- **上游依赖**：`codex-sandboxing`（权限策略转换）、`codex-protocol`（权限模型、审批策略）
- **内部依赖**：`tools/registry`（ToolHandler trait）、`tools/sandboxing`（审批上下文）、`tools/runtimes/`（运行时执行）、`tools/orchestrator`（编排器）、`agent/`（AgentControl）、`unified_exec/`（进程管理）、`mcp_connection_manager`（MCP 连接）
- **被依赖方**：`tools/registry.rs`（注册表注册处理器）、`tools/spec.rs`（工具规格构建时引用处理器名称）

## 核心接口/API

| 接口/类型 | 说明 |
|---|---|
| `ShellHandler` | Shell 工具处理器（Responses API shell_tool 格式） |
| `ShellCommandHandler` | Shell 命令处理器（function call 格式） |
| `ApplyPatchHandler` | 补丁应用处理器 |
| `McpHandler` | MCP 工具调用处理器 |
| `UnifiedExecHandler` | 统一执行工具处理器 |
| `ToolSearchHandler` | 工具搜索处理器 |
| `ToolSuggestHandler` | 工具建议处理器 |
