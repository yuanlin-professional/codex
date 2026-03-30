# `bwrap_tests.rs` — 测试模块

## 测试覆盖范围

测试 Bubblewrap (bwrap) 系统工具的检测逻辑，包括缺失告警、搜索路径优先级和工作区本地 bwrap 的排除。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `system_bwrap_warning_reports_missing_system_bwrap` | 系统中无 bwrap 时应生成包含 "could not find system bubblewrap" 的警告 |
| `system_bwrap_warning_skips_too_old_system_bwrap` | 即使 bwrap 不支持 `--argv0`，也不应产生警告（不再检查版本） |
| `finds_first_executable_bwrap_in_joined_search_path` | 在搜索路径中找到第一个可执行的 bwrap（跳过不可执行的同名文件） |
| `skips_workspace_local_bwrap_in_joined_search_path` | 工作目录下的 bwrap 应被跳过，优先使用受信任目录中的 bwrap |
