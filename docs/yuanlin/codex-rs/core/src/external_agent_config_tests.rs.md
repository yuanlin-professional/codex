# `external_agent_config_tests.rs` — 测试模块

## 测试覆盖范围

该测试模块覆盖外部代理配置（ExternalAgentConfig）的检测（detect）和导入（import）功能，包括从 Claude 主目录迁移配置文件（settings.json）、技能目录（skills）和 AGENTS.md 文件到 Codex 主目录，以及仓库级别的 CLAUDE.md 到 AGENTS.md 的迁移。验证术语替换（Claude -> Codex）、重复检测跳过、空目标覆盖、非空目标保留等边界行为。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `detect_home_lists_config_skills_and_agents_md` | 检测主目录时列出配置、技能和 AGENTS.md 三类可迁移项 |
| `detect_repo_lists_agents_md_for_each_cwd` | 检测仓库目录时为每个工作目录列出 AGENTS.md 迁移项 |
| `import_home_migrates_supported_config_fields_skills_and_agents_md` | 导入主目录时正确迁移支持的配置字段、技能文件夹和 AGENTS.md，并进行术语替换 |
| `import_home_skips_empty_config_migration` | 当配置无可迁移字段时跳过生成 config.toml |
| `detect_home_skips_config_when_target_already_has_supported_fields` | 目标已有受支持配置字段时跳过配置检测 |
| `detect_home_skips_skills_when_all_skill_directories_exist` | 所有技能目录已存在时跳过技能检测 |
| `import_repo_agents_md_rewrites_terms_and_skips_non_empty_targets` | 导入仓库 AGENTS.md 时重写术语，且跳过非空的目标文件 |
| `import_repo_agents_md_overwrites_empty_targets` | 当目标 AGENTS.md 为空或仅含空白时进行覆盖写入 |
| `detect_repo_prefers_non_empty_dot_claude_agents_source` | 检测时优先选择 .claude/CLAUDE.md 中非空的源文件 |
| `import_repo_uses_non_empty_dot_claude_agents_source` | 导入时优先使用 .claude/CLAUDE.md 中非空的源内容 |
| `migration_metric_tags_for_skills_include_skills_count` | 技能迁移的指标标签包含技能数量信息 |
| `import_skills_returns_only_new_skill_directory_count` | 导入技能时仅返回新复制的技能目录计数 |
