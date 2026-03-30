# `edit.rs` -- 配置编辑与持久化引擎

## 功能概述
提供对 `config.toml` 文件的编辑和持久化能力。定义了 `ConfigEdit` 枚举表示各种离散的配置修改操作，并通过 `ConfigDocument` 内部结构在 TOML 文档上执行这些操作，保留原有注释和格式。支持同步（blocking）和异步两种持久化方式，以及通过 `ConfigEditsBuilder` 流式构建批量编辑。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `ConfigEdit` | enum | 配置修改操作的完整集合，包括 `SetModel`、`SetServiceTier`、`SetModelPersonality`、`ReplaceMcpServers`、`SetSkillConfig`、`SetPath`、`ClearPath`、`SetProjectTrustLevel` 等变体 |
| `ConfigDocument` | struct (private) | 封装 `DocumentMut` 和 profile 信息，提供 `apply()` 方法执行编辑 |
| `ConfigEditsBuilder` | struct | 流式构建器，批量收集配置编辑后原子性应用 |
| `SkillConfigSelector` | enum (private) | Skill 配置选择器，支持按 `Name` 或 `Path` 定位 |
| `Scope` | enum (private) | 写入范围：`Global`（根级）或 `Profile`（当前 profile 作用域） |
| `TraversalMode` | enum (private) | TOML 路径遍历模式：`Create`（自动创建中间表）或 `Existing`（仅遍历已有表） |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `apply_blocking` | `fn(codex_home, profile, edits) -> Result<()>` | 同步模式：读取 config.toml、应用编辑列表、原子性写回 |
| `apply` | `async fn(codex_home, profile, edits) -> Result<()>` | 异步模式：将 blocking 操作 offload 到阻塞线程 |
| `ConfigDocument::apply` | `fn(&mut self, edit) -> Result<bool>` | 在文档上执行单个配置编辑，返回是否发生修改 |
| `ConfigDocument::replace_mcp_servers` | `fn(&mut self, servers) -> bool` | 替换整个 `[mcp_servers]` 表，保留 inline table 的注释和格式 |
| `ConfigDocument::set_skill_config` | `fn(&mut self, selector, enabled) -> bool` | 设置或清除 `[[skills.config]]` 条目 |
| `syntax_theme_edit` | `fn(name) -> ConfigEdit` | 生成 `[tui].theme` 的编辑操作 |
| `status_line_items_edit` | `fn(items) -> ConfigEdit` | 生成 `[tui].status_line` 的编辑操作 |
| `model_availability_nux_count_edits` | `fn(shown_count) -> Vec<ConfigEdit>` | 生成模型可用性 NUX 计数的编辑操作组 |

## 依赖关系
- **引用的 crate**: `toml_edit`, `anyhow`, `codex_config`, `codex_features`, `codex_protocol`, `tokio`
- **引用的模块**: `crate::config::types`, `crate::path_utils`
- **被引用方**: `config/service.rs`（配置服务层调用），TUI 层的配置持久化

## 实现备注
- `document_helpers` 子模块封装了 TOML 文档操作的工具函数，包括 inline table 与显式 table 之间的转换、MCP 服务器配置的序列化等。
- `preserve_decor` 方法在替换值时保留原有的 TOML 装饰信息（注释、空白），确保编辑后文件格式变化最小。
- Profile 作用域的写入通过 `scoped_segments` 自动在路径前添加 `profiles.<name>` 前缀。
- `set_skill_config` 使用 `ArrayOfTables`（`[[skills.config]]`）存储禁用的 skill，启用时删除条目；当整个 skills 表为空时清理根键。
