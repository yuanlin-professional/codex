# `live_reload.rs` — 测试模块

## 测试覆盖范围
技能（Skills）的实时重载功能，验证技能文件变更后缓存刷新并在下一轮对话中注入更新后的技能内容。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `live_skills_reload_refreshes_skill_cache_after_skill_change` | 修改 SKILL.md 文件后，技能缓存刷新并在下一轮请求中包含更新后的技能内容 |
