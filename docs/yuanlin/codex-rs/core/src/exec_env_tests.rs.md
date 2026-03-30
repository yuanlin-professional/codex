# `exec_env_tests.rs` — 测试模块

## 测试覆盖范围

该测试模块覆盖 Shell 执行环境变量填充（populate_env）的逻辑，包括不同继承策略（All/Core/None）下的变量继承行为、默认排除规则（过滤含 KEY/SECRET/TOKEN 的变量）、include_only 白名单过滤、set 覆盖/新增变量、以及 CODEX_THREAD_ID 环境变量的注入与省略。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `test_core_inherit_defaults_keep_sensitive_vars` | 验证默认 Core 继承策略保留敏感变量（API_KEY、SECRET_TOKEN 等） |
| `test_core_inherit_with_default_excludes_enabled` | 验证启用默认排除规则时过滤含 KEY/SECRET/TOKEN 的变量 |
| `test_include_only` | 验证 include_only 白名单仅保留匹配模式的变量 |
| `test_set_overrides` | 验证 set 字段能新增或覆盖环境变量 |
| `populate_env_inserts_thread_id` | 验证提供 thread_id 时插入 CODEX_THREAD_ID 环境变量 |
| `populate_env_omits_thread_id_when_missing` | 验证未提供 thread_id 时不插入 CODEX_THREAD_ID 环境变量 |
| `test_inherit_all` | 验证 All 继承策略保留所有环境变量 |
| `test_inherit_all_with_default_excludes` | 验证 All 继承策略配合默认排除规则正确过滤敏感变量 |
| `test_core_inherit_respects_case_insensitive_names_on_windows` | (仅 Windows) 验证 Core 继承在 Windows 上正确处理大小写不敏感的变量名 |
| `test_inherit_none` | 验证 None 继承策略不继承任何环境变量，仅保留 set 中的变量 |
