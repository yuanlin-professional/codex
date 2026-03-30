# `role.rs` — Agent 角色配置层应用

## 功能概述

该模块负责在 spawn agent 时将角色（role）配置层叠加到现有 session 配置上。角色在 spawn 时选择，使用与 `config.toml` 相同的配置机制加载。模块解析内置和用户自定义的角色文件，将角色作为高优先级配置层插入，同时保留调用方当前的 profile/provider 选择（除非角色显式接管模型选择）。模块本身不决定何时 spawn 子 agent 或使用哪个角色——这由多 agent 工具处理器负责。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `DEFAULT_ROLE_NAME` | const | 默认角色名 `"default"`，当未指定角色时使用 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `apply_role_to_config` | `async fn(config, role_name) -> Result<(), String>` | 将命名角色层应用到配置，保留调用方的 profile/provider |
| `resolve_role_config` | `fn(config, role_name) -> Option<&AgentRoleConfig>` | 解析角色配置：优先查找用户自定义角色，回退到内置角色 |
| `preservation_policy` | `fn(config, role_toml) -> (bool, bool)` | 判断是否应保留当前 profile 和 provider |
| `spawn_tool_spec::build` | `fn(user_roles) -> String` | 构建 spawn-agent 工具的描述文本，列出所有可用角色 |

### `reload` 子模块

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `build_next_config` | `fn(config, role_toml, preserve_profile, preserve_provider) -> Result<Config>` | 将角色层合并到配置层栈中并重新构建完整配置 |

### `built_in` 子模块

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `configs` | `fn() -> &'static BTreeMap<String, AgentRoleConfig>` | 返回内置角色声明的缓存映射 |
| `config_file_contents` | `fn(path) -> Option<&'static str>` | 将内置角色配置文件路径解析为嵌入内容 |

## 依赖关系

- **引用的 crate**: `anyhow`、`toml`、`codex_app_server_protocol`
- **引用的内部模块**: `crate::config`（`AgentRoleConfig`、`Config`、`ConfigOverrides`、`agent_roles`）、`crate::config_loader`
- **被引用方**: `agent::control`（spawn 时应用角色）

## 实现备注

- **内置角色**: 目前包含 `default`（默认 agent）、`explorer`（探索者，用于代码库查询）、`worker`（工人，用于执行和生产工作）。`awaiter` 角色暂时被移除。
- **Profile 保留逻辑**: 如果角色未显式设置 `profile` 或 `model_provider`，则保留调用方当前的选择，避免 spawn 的 agent 静默回退到默认 provider。
- **配置层优先级**: 角色层以 `SessionFlags` 优先级插入，可覆盖持久化配置但低于 CLI 覆盖。
- **角色文件解析**: 内置角色使用 `include_str!` 嵌入；用户自定义角色从文件系统读取，支持 `agent_roles` 文件格式的元数据字段（`name`、`description`、`nickname_candidates`）剥离。
- **工具描述生成**: `spawn_tool_spec::build` 去重处理用户自定义与内置角色的重叠，并在描述中标注锁定的模型和推理努力度设置。
