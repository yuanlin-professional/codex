# `realtime_context_tests.rs` — 测试模块

## 测试覆盖范围

测试实时上下文构建功能，包括工作区目录树（workspace section）的生成逻辑和近期工作会话（recent work section）的分组汇总逻辑。验证 `build_workspace_section_with_user_root` 和 `build_recent_work_section` 在不同目录结构与线程元数据输入下的行为。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `workspace_section_requires_meaningful_structure` | 空临时目录不包含有意义的结构时，工作区 section 应返回 `None` |
| `workspace_section_includes_tree_when_entries_exist` | 当工作目录下存在子目录和文件时，生成的 section 应包含目录树信息 |
| `workspace_section_includes_user_root_tree_when_distinct` | 当 `user_root` 与工作目录不同时，section 应额外包含用户根目录树，且排除隐藏文件（如 `.zshrc`） |
| `recent_work_section_groups_threads_by_cwd` | 近期工作会话按 git 仓库分组显示，仓库外的会话按独立目录分组，并正确展示会话数量和用户消息摘要 |
