# `skill_dependencies.rs` — 技能 MCP 依赖自动安装

## 功能概述

`skill_dependencies.rs` 实现了技能（Skill）所依赖的 MCP 服务器的自动检测、用户提示和安装流程。当用户使用的技能声明了 MCP 工具依赖但当前未安装对应的 MCP 服务器时，该模块会识别缺失的依赖，在非全权限模式下向用户发起安装确认提示，然后将缺失的 MCP 服务器写入全局配置、执行 OAuth 登录（如需要），并刷新当前会话的 MCP 连接。

## 关键结构体/枚举

无独立结构体/枚举定义（使用外部类型）。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `maybe_prompt_and_install_mcp_dependencies` | `async fn maybe_prompt_and_install_mcp_dependencies(sess, turn_context, cancellation_token, mentioned_skills)` | 入口函数：检测缺失依赖、提示用户、触发安装 |
| `maybe_install_mcp_dependencies` | `async fn maybe_install_mcp_dependencies(sess, turn_context, config, mentioned_skills)` | 执行实际安装：写入配置、OAuth 登录、刷新 MCP 连接 |
| `collect_missing_mcp_dependencies` | `fn collect_missing_mcp_dependencies(mentioned_skills, installed) -> HashMap<...>` | 比对技能声明的 MCP 依赖与已安装服务器，收集缺失项 |
| `should_install_mcp_dependencies` | `async fn should_install_mcp_dependencies(sess, turn_context, missing, cancellation_token) -> bool` | 在非全权限模式下弹出用户确认对话框 |
| `filter_prompted_mcp_dependencies` | `async fn filter_prompted_mcp_dependencies(sess, missing) -> HashMap<...>` | 过滤掉本次会话已提示过的依赖 |
| `canonical_mcp_server_key` | `fn canonical_mcp_server_key(name, config) -> String` | 生成 MCP 服务器的规范化键（基于传输方式和标识符） |
| `canonical_mcp_dependency_key` | `fn canonical_mcp_dependency_key(dependency) -> Result<String, String>` | 生成技能依赖的规范化键 |
| `mcp_dependency_to_server_config` | `fn mcp_dependency_to_server_config(dependency) -> Result<McpServerConfig, String>` | 将技能的工具依赖转换为 MCP 服务器配置 |

## 依赖关系

- **引用的 crate**: `codex_protocol`, `codex_rmcp_client`, `codex_features`, `tracing`, `tokio_util`
- **引用的内部模块**: `auth`（OAuth 登录支持）、`Session`、`TurnContext`、`Config`、`ConfigEditsBuilder`、`McpServerConfig`、`SkillMetadata`、`SkillToolDependency`
- **被引用方**: `mcp/mod.rs` 通过 `pub(crate) use` 导出 `maybe_prompt_and_install_mcp_dependencies`，在技能选择流程中调用

## 实现备注

- 使用"规范化键"（canonical key）机制比对已安装服务器和依赖声明，避免因名称别名导致重复安装（如同一 URL 但不同名称）
- 全权限模式（`AskForApproval::Never` 且 `DangerFullAccess` 或 `ExternalSandbox`）下自动安装，无需用户确认
- 安装流程支持两种传输类型：`streamable_http` 和 `stdio`
- OAuth 登录失败后，如果 scope 来源是自动发现的（`Discovered`），会尝试不带 scope 重试
- 安装完成后通过 `refresh_mcp_servers_now` 刷新会话中的 MCP 连接，合并全局和仓库级别的服务器配置
- 仅支持第一方 originator 客户端（`is_first_party_originator`），且需要 `SkillMcpDependencyInstall` 特性标志启用
- 同一会话中已提示过的依赖不会再次提示（通过 `mcp_dependency_prompted` 集合跟踪）
