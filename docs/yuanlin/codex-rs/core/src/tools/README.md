# tools/ -- 工具系统

## 功能概述

`tools/` 是 Codex 核心工具系统的实现目录，负责工具的注册、路由、执行编排和沙箱集成。它是 AI 代理与外部环境（文件系统、Shell、MCP 服务器等）交互的核心桥梁。所有工具调用（function call、MCP 工具调用、Shell 命令执行等）均通过此模块的统一管道完成，包括审批流程、沙箱策略选择、网络审批以及执行输出的格式化与截断。

## 目录结构

```
tools/
├── mod.rs                   # 模块入口，导出子模块，定义输出格式化函数和遥测常量
├── spec.rs                  # 工具规格构建器，根据配置和模型能力构建工具集
├── registry.rs              # 工具注册表，管理 ToolHandler trait 和工具分发
├── router.rs                # ToolRouter：工具路由器，负责匹配工具调用到处理器
├── orchestrator.rs          # ToolOrchestrator：审批 -> 沙箱选择 -> 执行 -> 重试的核心编排器
├── context.rs               # ToolInvocation / ToolPayload / ToolOutput 等调用上下文类型
├── events.rs                # 工具事件发射器（ExecCommandBegin/End、PatchApply 等）
├── parallel.rs              # ToolCallRuntime：并行工具调用的运行时封装
├── discoverable.rs          # 可发现工具类型（连接器/插件），用于工具建议
├── network_approval.rs      # 网络审批服务（即时/延迟模式）
├── sandboxing.rs            # 审批存储、沙箱 trait（ToolRuntime/Sandboxable/Approvable）
├── handlers/                # 具体工具处理器实现（Shell、apply_patch、MCP、多代理等）
│   ├── shell.rs             # Shell 命令执行处理器
│   ├── apply_patch.rs       # 补丁应用处理器
│   ├── mcp.rs               # MCP 工具调用处理器
│   ├── unified_exec.rs      # 统一执行进程管理工具
│   ├── multi_agents.rs      # 多代理工具（spawn/wait/send/list）v1
│   ├── multi_agents_v2.rs   # 多代理工具 v2
│   ├── tool_search.rs       # 工具搜索处理器
│   ├── tool_suggest.rs      # 工具建议处理器
│   ├── view_image.rs        # 图片查看处理器
│   └── ...                  # 其他工具处理器
├── runtimes/                # 工具运行时（沙箱执行层）
│   ├── shell/               # Shell 运行时，包含 Unix 特权提升
│   └── unified_exec.rs      # 统一执行运行时
├── js_repl/                 # JavaScript REPL 内核（meriyah 解析器）
├── code_mode/               # 代码模式工具（execute_handler / wait_handler）
├── spec_tests.rs            # 工具规格测试
├── registry_tests.rs        # 注册表测试
├── router_tests.rs          # 路由器测试
├── context_tests.rs         # 上下文测试
├── sandboxing_tests.rs      # 沙箱测试
└── network_approval_tests.rs # 网络审批测试
```

## 依赖关系

- **上游依赖**：`codex-tools`（工具规格定义 `ResponsesApiTool`、工具创建函数）、`codex-sandboxing`（沙箱管理器 `SandboxManager`、沙箱类型 `SandboxType`）、`codex-protocol`（协议类型 `SandboxPolicy`、`ReviewDecision`）、`codex-network-proxy`（网络代理）
- **内部依赖**：`config/`（工具配置）、`sandboxing/`（ExecRequest 适配）、`unified_exec/`（进程管理）、`mcp/`（MCP 工具名称解析）、`agent/`（多代理控制）、`guardian/`（审批守卫）、`plugins/`（可发现插件）
- **被依赖方**：`tasks/`（RegularTask 调用 ToolRouter 执行工具）、`codex/`（Session 初始化 ToolRouter）

## 核心接口/API

| 接口/类型 | 说明 |
|---|---|
| `ToolRouter` | 顶层工具路由器，持有注册表和工具规格列表，提供 `from_config()` 构造和 `handle()` 分发 |
| `ToolRegistry` | 工具注册表，存储 `ToolHandler` 映射，按名称查找处理器 |
| `ToolHandler` trait | 工具处理器 trait，定义 `handle()`、`is_mutating()`、`pre/post_tool_use_payload()` |
| `ToolOrchestrator` | 编排器：审批 -> 沙箱选择 -> 执行尝试 -> 沙箱拒绝重试 |
| `ToolRuntime` trait | 沙箱运行时 trait，定义 `run()`、`network_approval_spec()` |
| `ToolInvocation` | 工具调用上下文，包含 session、turn、call_id、payload |
| `ToolPayload` | 工具载荷枚举：`Function`、`Mcp`、`LocalShell`、`ToolSearch`、`Custom` |
| `format_exec_output_for_model_structured()` | 将执行输出格式化为结构化 JSON 返回给模型 |
| `format_exec_output_for_model_freeform()` | 将执行输出格式化为自由文本返回给模型 |
