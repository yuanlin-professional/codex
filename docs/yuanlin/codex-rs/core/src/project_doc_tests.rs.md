# `project_doc_tests.rs` — 测试模块

## 测试覆盖范围

项目文档（AGENTS.md）发现与加载逻辑，包括文档缺失/存在/超限截断、仓库根目录搜索、字节限制禁用、JS REPL 指令追加、系统指令合并、根目录与嵌套目录文档拼接、覆盖文件优先级、回退文件名、项目根标记、技能不追加到项目文档、Apps 特性不生成用户指令。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `no_doc_file_returns_none` | AGENTS.md 缺失时返回 None |
| `doc_smaller_than_limit_is_returned` | 小于限制的文档完整返回 |
| `doc_larger_than_limit_is_truncated` | 超过限制的文档被截断 |
| `finds_doc_in_repo_root` | 在仓库根目录（由 .git 标识）搜索并找到 AGENTS.md |
| `zero_byte_limit_disables_docs` | 字节限制为零时禁用项目文档 |
| `js_repl_instructions_are_appended_when_enabled` | 启用 JS REPL 时追加相关指令 |
| `js_repl_tools_only_instructions_are_feature_gated` | JS REPL 仅工具指令受特性门控 |
| `js_repl_image_detail_original_does_not_change_instructions` | JS REPL 图片详情原始不改变指令 |
| `merges_existing_instructions_with_project_doc` | 合并已有指令与项目文档 |
| `keeps_existing_instructions_when_doc_missing` | 文档缺失时保留已有指令 |
| `concatenates_root_and_cwd_docs` | 拼接仓库根目录和工作目录的文档 |
| `project_root_markers_are_honored_for_agents_discovery` | 项目根标记用于 AGENTS 文档发现 |
| `agents_local_md_preferred` | AGENTS.override.md 优先于 AGENTS.md |
| `uses_configured_fallback_when_agents_missing` | AGENTS.md 缺失时使用配置的回退文件 |
| `agents_md_preferred_over_fallbacks` | AGENTS.md 优先于回退文件 |
| `skills_are_not_appended_to_project_doc` | 技能不追加到项目文档 |
| `apps_feature_does_not_emit_user_instructions_by_itself` | Apps 特性本身不生成用户指令 |
| `apps_feature_does_not_append_to_project_doc_user_instructions` | Apps 特性不追加到项目文档用户指令 |
