# `agent_roles.rs` -- Agent 角色配置加载与管理

## 功能概述
该文件负责从多层配置堆栈中加载和合并 Agent 角色定义。支持从 TOML 配置文件中声明式定义角色，也支持从 `agents/` 目录中自动发现角色文件。多个配置层按优先级从低到高合并，同层内禁止同名角色重复。解析过程中的错误会被收集为启动警告，而非直接中断加载。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `RawAgentRoleFileToml` | struct | 从 `.toml` 角色文件反序列化的原始结构，包含 `name`、`description`、`nickname_candidates` 和扁平化的 `ConfigToml` |
| `ResolvedAgentRoleFile` | struct | 解析后的角色文件结果，包含角色名、描述、昵称候选列表和 TOML 配置值 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `load_agent_roles` | `fn(cfg, config_layer_stack, startup_warnings) -> Result<BTreeMap<String, AgentRoleConfig>>` | 主入口：从配置层堆栈中加载所有 Agent 角色，按优先级合并 |
| `load_agent_roles_without_layers` | `fn(cfg) -> Result<BTreeMap<String, AgentRoleConfig>>` | 无配置层时的回退加载逻辑 |
| `parse_agent_role_file_contents` | `fn(contents, role_file_label, config_base_dir, role_name_hint) -> Result<ResolvedAgentRoleFile>` | 解析角色文件内容为 `ResolvedAgentRoleFile` |
| `read_declared_role` | `fn(declared_role_name, role_toml) -> Result<(String, AgentRoleConfig)>` | 从声明的角色条目读取并解析完整角色配置 |
| `discover_agent_roles_in_dir` | `fn(agents_dir, declared_role_files, startup_warnings) -> Result<BTreeMap<String, AgentRoleConfig>>` | 从 `agents/` 目录递归发现 `.toml` 角色文件 |
| `normalize_agent_role_nickname_candidates` | `fn(field_label, nickname_candidates) -> Result<Option<Vec<String>>>` | 校验并规范化昵称候选列表（非空、无重复、仅限 ASCII 字母数字及分隔符） |
| `merge_missing_role_fields` | `fn(role, fallback)` | 将低优先级角色的缺失字段合并到高优先级角色中 |

## 依赖关系
- **引用的 crate**: `serde`, `toml`, `codex_utils_absolute_path`, `std::collections::{BTreeMap, BTreeSet}`
- **引用的模块**: `super::{AgentRoleConfig, AgentRoleToml, AgentsToml, ConfigToml}`, `crate::config_loader::{ConfigLayerStack, ConfigLayerStackOrdering}`
- **被引用方**: 被 `config/mod.rs` 中的配置构建流程调用

## 实现备注
- 多配置层合并时采用"低优先级优先遍历、高优先级覆盖"的策略。对于同一角色名，高优先级层的字段优先，缺失字段从低优先级层补充。
- `collect_agent_role_files_recursive` 递归搜索目录中所有 `.toml` 文件，结果排序后返回以保证确定性。
- 所有解析错误通过 `push_agent_role_warning` 收集为警告而非 fatal error，确保单个角色文件的问题不会阻止其他角色的加载。
- `validate_agent_role_file_developer_instructions` 对自动发现的角色文件（无声明式引用）要求必须定义 `developer_instructions`。
