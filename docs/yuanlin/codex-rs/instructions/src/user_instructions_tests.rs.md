# `user_instructions_tests.rs` — 测试模块

## 测试覆盖范围

验证用户指令和技能指令的序列化格式和片段匹配。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `test_user_instructions` | UserInstructions 序列化为正确的 AGENTS.md 格式 ResponseItem |
| `test_is_user_instructions` | AGENTS_MD_FRAGMENT 正确匹配/不匹配文本 |
| `test_skill_instructions` | SkillInstructions 序列化为正确的 `<skill>` XML 格式 |
| `test_is_skill_instructions` | SKILL_FRAGMENT 正确匹配/不匹配文本 |
