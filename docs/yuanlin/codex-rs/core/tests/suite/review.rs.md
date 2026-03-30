# `review.rs` -- 测试模块

## 测试覆盖范围
测试 Codex 的代码审查（review）功能，包括审查提示词构建、审查结果解析和渲染、多文件审查流程以及审查目标配置（PR diff、特定文件等）。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `review_prompt_includes_diff` | 审查提示词包含 diff 内容 |
| `review_output_rendering` | 审查结果 JSON 正确渲染为人类可读格式 |
| `review_mode_integration` | 端到端审查模式集成测试 |
| `review_findings_parsing` | 审查发现项（findings）的解析验证 |
