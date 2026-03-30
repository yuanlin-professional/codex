# `prompts_tests.rs` -- 测试模块

## 测试覆盖范围

覆盖 `prompts.rs` 中的三个提示词构建函数，验证模板渲染、rollout 内容截断以及 memory_summary 读取逻辑。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `build_stage_one_input_message_truncates_rollout_using_model_context_window` | 验证 rollout 内容按模型上下文窗口正确截断，保留头尾并插入截断提示 |
| `build_stage_one_input_message_uses_default_limit_when_model_context_window_missing` | 验证模型无上下文窗口时使用默认 150,000 token 限制 |
| `build_consolidation_prompt_renders_embedded_template` | 验证合并提示词模板正确渲染目录结构和输入统计 |
| `build_memory_tool_developer_instructions_renders_embedded_template` | 验证读取 memory_summary.md 并正确渲染为开发者指令 |
