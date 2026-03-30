# `git_info_tests.rs` — 测试模块

## 测试覆盖范围

Git 信息收集功能，包括最近提交查询、Git 仓库信息收集（提交哈希/分支/远程 URL）、工作树状态检测（是否有变更、与远程 diff）、Git 项目信任根解析（worktree/gitdir 指针）、GitInfo 序列化。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `test_recent_commits_non_git_directory_returns_empty` | 非 Git 目录下查询最近提交返回空 |
| `test_recent_commits_orders_and_limits` | 最近提交按时间倒序排列并限制数量 |
| `test_collect_git_info_non_git_directory` | 非 Git 目录下收集 Git 信息返回 None |
| `test_collect_git_info_git_repository` | Git 仓库中收集提交哈希和分支信息 |
| `test_collect_git_info_with_remote` | 带远程仓库时收集远程 URL |
| `test_collect_git_info_detached_head` | 分离 HEAD 状态下分支为 None |
| `test_collect_git_info_with_branch` | 在特性分支上收集正确的分支名称 |
| `test_get_has_changes_non_git_directory_returns_none` | 非 Git 目录下检测变更返回 None |
| `test_get_has_changes_clean_repo_returns_false` | 干净仓库检测变更返回 false |
| `test_get_has_changes_with_tracked_change_returns_true` | 已跟踪文件有修改时返回 true |
| `test_get_has_changes_with_untracked_change_returns_true` | 有未跟踪文件时返回 true |
| `test_get_git_working_tree_state_clean_repo` | 干净仓库的工作树状态 diff 为空 |
| `test_get_git_working_tree_state_with_changes` | 有修改时工作树状态包含 diff |
| `test_get_git_working_tree_state_branch_fallback` | 无跟踪分支时回退到其他远程分支 |
| `resolve_root_git_project_for_trust_returns_none_outside_repo` | 仓库外解析信任根返回 None |
| `resolve_root_git_project_for_trust_regular_repo_returns_repo_root` | 普通仓库解析信任根返回仓库根目录 |
| `resolve_root_git_project_for_trust_detects_worktree_and_returns_main_root` | 检测 worktree 并返回主仓库根目录 |
| `resolve_root_git_project_for_trust_detects_worktree_pointer_without_git_command` | 不调用 git 命令也能检测 worktree 指针 |
| `resolve_root_git_project_for_trust_non_worktrees_gitdir_returns_none` | 非 worktrees 的 gitdir 路径返回 None |
| `test_get_git_working_tree_state_unpushed_commit` | 未推送提交时工作树状态包含 diff |
| `test_git_info_serialization` | GitInfo 序列化为预期 JSON 格式 |
| `test_git_info_serialization_with_nones` | GitInfo 中 None 字段在序列化时被省略 |
