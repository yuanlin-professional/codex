# `skills.rs` -- 测试模块

## 测试覆盖范围
测试 skill 系统的核心功能，包括 skill 指令注入、skill 加载错误处理，以及系统内置 skill 的安装与列举。仅在非 Windows 系统上运行。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `user_turn_includes_skill_instructions` | 当用户 turn 中引用 `$demo` skill 时，请求 input 中包含 `<skill>` XML 标签及 skill 正文内容和路径 |
| `skill_load_errors_surface_in_session_configured` | SKILL.md 格式错误的 skill 不会被加载，ListSkills 响应中包含该 skill 的加载错误信息 |
| `list_skills_includes_system_cache_entries` | 系统内置 skill（如 skill-creator）在构建后自动安装到 `.system` 目录，ListSkills 返回中包含系统 skill 且 scope 为 System |
