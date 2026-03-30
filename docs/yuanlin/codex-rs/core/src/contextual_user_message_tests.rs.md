# `contextual_user_message_tests.rs` — 测试模块

## 测试覆盖范围

测试上下文用户消息片段的识别与分类逻辑，包括环境上下文、AGENTS.md 指令、子代理通知、Hook 提示消息等不同类型片段的检测，以及 memory 排除判定和 XML 转义的往返正确性。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `detects_environment_context_fragment` | 识别 `<environment_context>` 标签包裹的环境上下文片段 |
| `detects_agents_instructions_fragment` | 识别 `# AGENTS.md instructions` 开头的指令片段 |
| `detects_subagent_notification_fragment_case_insensitively` | 子代理通知片段的标签匹配不区分大小写 |
| `ignores_regular_user_text` | 普通用户文本不应被识别为上下文片段 |
| `classifies_memory_excluded_fragments` | 正确分类哪些片段应从 memory 中排除（AGENTS.md 指令和 skill 片段排除，环境上下文和子代理通知不排除） |
| `detects_hook_prompt_fragment_and_roundtrips_escaping` | Hook 提示消息被正确识别为上下文片段，且 XML 特殊字符的转义和反转义能正确往返 |
