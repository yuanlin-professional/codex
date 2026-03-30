# `spec.rs` — 工具规格构建与注册

## 功能概述
工具系统的配置和规格构建中心，约 1170 行。定义了 `ToolsConfig`（工具配置）和 `build_specs_with_discoverable_tools`（核心构建函数），负责根据 feature flags、模型能力和会话配置动态组装所有可用工具的规格定义和处理器注册。支持的工具类型包括：shell/shell_command/unified_exec、apply_patch、js_repl、code_mode、web_search、image_gen、view_image、multi_agent（v1/v2）、tool_search/tool_suggest、MCP、动态工具等。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ToolsConfig` | struct | 工具配置：所有 feature 开关、shell 类型、apply_patch 类型、agent 角色等 |
| `ToolsConfigParams<'a>` | struct | 配置构建参数：model_info、features、session_source、sandbox_policy 等 |
| `ShellCommandBackendConfig` | enum | Shell 命令后端：Classic / ZshFork |
| `UnifiedExecShellMode` | enum | Unified Exec Shell 模式：Direct / ZshFork(ZshForkConfig) |
| `ZshForkConfig` | struct | ZshFork 配置：shell_zsh_path + main_execve_wrapper_exe |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ToolsConfig::new` | `fn(params) -> Self` | 从参数构建工具配置，应用 feature flags 和模型能力 |
| `build_specs_with_discoverable_tools` | `fn(config, mcp_tools, app_tools, discoverable_tools, dynamic_tools) -> ToolRegistryBuilder` | 核心构建函数，注册所有工具的 spec 和 handler |
| `create_tool_search_tool` | `fn(app_tools) -> ToolSpec` | 构建 tool_search 工具规格，生成动态描述 |
| `create_tool_suggest_tool` | `fn(discoverable_tools) -> ToolSpec` | 构建 tool_suggest 工具规格 |

## 依赖关系
- **引用的 crate**: `codex_tools`, `codex_features`, `codex_protocol`, `codex_utils_template`
- **内部依赖**: `crate::tools::handlers::*`, `crate::tools::registry`, `crate::tools::discoverable`
- **被引用方**: `router.rs` 中的 `ToolRouter::from_config`

## 实现备注
- Code Mode 启用时，先递归构建嵌套工具列表（排除 Code Mode 自身），生成可用工具描述
- `code_mode_enabled` 时所有 spec 都通过 `augment_tool_spec_for_code_mode` 增强
- shell 类型选择优先级：Feature(ShellTool) -> Feature(ShellZshFork) -> Feature(UnifiedExec) -> 模型默认
- Windows 上限制 unified_exec 的使用条件（需要 ConPTY 支持或特定沙箱策略）
- 工具搜索描述使用模板引擎动态渲染已启用的连接器列表
