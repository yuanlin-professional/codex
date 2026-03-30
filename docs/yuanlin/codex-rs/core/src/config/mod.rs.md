# `mod.rs` -- 配置模块主入口与 Config 核心结构体

## 功能概述
这是 `config` 模块的主文件，定义了 Codex 应用的核心配置结构体 `Config` 及其构建流程。负责将多层 TOML 配置（系统、用户、项目、CLI 覆盖）合并为最终的运行时配置对象。文件还定义了 `ConfigToml`（配置文件的反序列化目标）、`ConfigBuilder`（异步配置加载器）和 `ConfigOverrides`（运行时覆盖参数），以及配置加载、MCP 服务器合并、sandbox 策略确定、模型提供者解析等大量业务逻辑。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `Config` | struct | 最终的运行时配置对象，包含模型、sandbox 策略、MCP 服务器、权限、特性开关等所有配置项 |
| `ConfigToml` | struct | `config.toml` 的反序列化目标结构体，`#[serde(deny_unknown_fields)]` |
| `ConfigBuilder` | struct | 异步配置构建器，从 codex_home/cwd/CLI 覆盖等加载并合并配置层 |
| `ConfigOverrides` | struct | 运行时覆盖参数（cwd、zsh_path 等），用于测试和特殊场景 |
| `ProjectConfig` | struct | 项目级配置（`trust_level`） |
| `AgentRoleConfig` | struct | Agent 角色配置，包含描述、配置文件路径和昵称候选 |
| `AgentsToml` | struct | `[agents]` 表的反序列化结构 |
| `ToolsToml` | struct | `[tools]` 表的反序列化结构 |
| `Permissions` | struct | 编译后的权限配置（文件系统 sandbox 策略、网络 sandbox 策略等） |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ConfigBuilder::build` | `async fn(self) -> Result<Config>` | 主入口：异步加载配置层、合并、验证，构建最终 `Config` |
| `Config::load_from_base_config_with_overrides` | `fn(base_config, overrides, codex_home) -> Result<Config>` | 从基础 ConfigToml 和覆盖参数构建 Config |
| `set_project_trust_level_inner` | `fn(doc, path, level) -> Result<()>` | 在 TOML 文档中设置项目信任级别，处理 inline table 迁移 |
| `deserialize_config_toml_with_base` | `fn(value, base_dir) -> Result<ConfigToml>` | 以指定基目录解析 AbsolutePath 字段的 ConfigToml |

## 依赖关系
- **引用的 crate**: `codex_protocol`, `codex_config`, `codex_features`, `codex_network_proxy`, `codex_execpolicy`, `serde`, `schemars`, `toml`, `toml_edit`
- **子模块**: `agent_roles`, `edit`, `managed_features`, `network_proxy_spec`, `permissions`, `profile`, `schema`, `service`, `types`
- **被引用方**: 项目中几乎所有需要访问配置的模块

## 实现备注
- 配置加载遵循严格的优先级层次：system < user < project < CLI overrides < managed_config。
- MCP 服务器配置在合并时需要处理管理员需求（如强制禁用、强制启用）与用户配置的交互。
- sandbox 策略根据 `sandbox_mode`、`default_permissions` 和 `approval_policy` 三者综合确定。
- 文件超过 2800 行，是整个配置子系统的核心和最复杂的模块。
