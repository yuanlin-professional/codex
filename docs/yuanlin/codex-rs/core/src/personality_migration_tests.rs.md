# `personality_migration_tests.rs` — 测试模块

## 测试覆盖范围

测试个性化（personality）迁移功能 `maybe_migrate_personality` 的各种分支逻辑。验证在不同初始状态下（有无会话历史、有无迁移标记、有无显式个性化配置），迁移操作是否正确执行或跳过，以及迁移后配置文件和标记文件的状态是否符合预期。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `applies_when_sessions_exist_and_no_personality` | 当存在会话历史但未设置个性化时，执行迁移，写入默认 `Pragmatic` 个性化并创建标记文件 |
| `skips_when_marker_exists` | 当迁移标记文件已存在时，跳过迁移，不写入 config.toml |
| `skips_when_personality_explicit` | 当用户已显式设置个性化（如 `Friendly`）时，跳过迁移，保留原有配置并创建标记文件 |
| `skips_when_no_sessions` | 当不存在任何会话历史时，跳过迁移，创建标记文件但不生成 config.toml |
