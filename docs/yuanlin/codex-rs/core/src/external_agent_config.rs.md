# `external_agent_config.rs` — 外部代理配置迁移服务（Claude Code 配置导入）

## 功能概述

该文件实现了从 Claude Code（`.claude/`）配置格式向 Codex（`.codex/`）配置格式的检测与迁移功能。`ExternalAgentConfigService` 提供了 `detect` 和 `import` 两个核心操作：`detect` 扫描用户主目录和工作目录中的 Claude 配置，识别可迁移的项目（配置文件、技能目录、AGENTS.md）；`import` 则执行实际迁移，包括 JSON 到 TOML 的配置转换、技能目录递归拷贝，以及 CLAUDE.md 到 AGENTS.md 的内容重写（自动替换品牌术语）。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ExternalAgentConfigService` | struct | 迁移服务主体，持有 `codex_home` 和 `claude_home` 路径 |
| `ExternalAgentConfigDetectOptions` | struct | 检测选项：是否包含主目录、工作目录列表 |
| `ExternalAgentConfigMigrationItem` | struct | 单个迁移项：类型、描述、关联的工作目录 |
| `ExternalAgentConfigMigrationItemType` | enum | 迁移项类型：Config / Skills / AgentsMd / McpServerConfig |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ExternalAgentConfigService::detect` | `fn(&self, params) -> io::Result<Vec<MigrationItem>>` | 检测所有可迁移的配置项 |
| `ExternalAgentConfigService::import` | `fn(&self, items: Vec<MigrationItem>) -> io::Result<()>` | 执行迁移操作 |
| `import_config` | `fn(&self, cwd: Option<&Path>) -> io::Result<()>` | 将 `settings.json` 转换并合并到 `config.toml` |
| `import_skills` | `fn(&self, cwd: Option<&Path>) -> io::Result<usize>` | 递归拷贝技能目录，返回拷贝数量 |
| `import_agents_md` | `fn(&self, cwd: Option<&Path>) -> io::Result<()>` | 将 CLAUDE.md 重写为 AGENTS.md |
| `build_config_from_external` | `fn(settings: &JsonValue) -> io::Result<TomlValue>` | 从 Claude settings.json 提取可迁移字段（env、sandbox）转为 TOML |
| `merge_missing_toml_values` | `fn(existing: &mut TomlValue, incoming: &TomlValue) -> io::Result<bool>` | 递归合并 TOML 表，仅插入不存在的键 |
| `rewrite_claude_terms` | `fn(content: &str) -> String` | 将内容中的 "claude"/"claude code" 等术语替换为 "Codex" |
| `find_repo_root` | `fn(cwd: Option<&Path>) -> io::Result<Option<PathBuf>>` | 沿目录树向上查找 `.git` 标识的仓库根目录 |

## 依赖关系

- **引用的 crate**: `serde_json`（解析 Claude settings.json）、`toml`（生成和解析 Codex config.toml）、`codex_otel`（迁移指标上报）
- **被引用方**: TUI/CLI 层在启动时调用检测和迁移流程

## 实现备注

- `replace_case_insensitive_with_boundaries` 实现了带词边界检测的大小写不敏感替换，避免误替换子串（如不会把 "claudeapp" 中的 "claude" 替换掉）。
- 配置合并采用"仅插入缺失键"策略（`merge_missing_toml_values`），不会覆盖用户已有的配置值。
- 通过 `codex_otel` 上报检测和导入的指标计数，包括迁移类型和技能数量标签。
- 技能目录中的 `SKILL.md` 文件会在拷贝时经过术语重写处理。
