# `shell_snapshot_tests.rs` — 测试模块

## 测试覆盖范围

Shell 快照功能，包括快照前导内容剥离、快照文件名解析、bash 快照过滤无效导出和保留多行导出、快照文件创建与删除生命周期、快照生成路径唯一性、stdin 不继承、超时快照终止、各平台 shell 快照内容验证、过期快照清理。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `strip_snapshot_preamble_removes_leading_output` | 剥离快照前导输出内容 |
| `strip_snapshot_preamble_requires_marker` | 快照前导剥离要求标记存在 |
| `snapshot_file_name_parser_supports_legacy_and_suffixed_names` | 快照文件名解析器支持传统和带后缀的名称 |
| `bash_snapshot_filters_invalid_exports` | bash 快照过滤无效导出（PWD/NEXTEST/BAD-NAME） |
| `bash_snapshot_preserves_multiline_exports` | bash 快照保留多行环境变量导出 |
| `try_new_creates_and_deletes_snapshot_file` | 快照创建和删除文件生命周期 |
| `try_new_uses_distinct_generation_paths` | 快照使用不同的生成路径 |
| `snapshot_shell_does_not_inherit_stdin` | 快照 shell 不继承 stdin |
| `timed_out_snapshot_shell_is_terminated` | 超时的快照 shell 被终止 |
| `macos_zsh_snapshot_includes_sections` | macOS zsh 快照包含预期节段 |
| `linux_bash_snapshot_includes_sections` | Linux bash 快照包含预期节段 |
| `linux_sh_snapshot_includes_sections` | Linux sh 快照包含预期节段 |
| `windows_powershell_snapshot_includes_sections` | Windows PowerShell 快照包含预期节段 |
| `cleanup_stale_snapshots_removes_orphans_and_keeps_live` | 清理过期快照时移除孤立快照保留活跃快照 |
| `cleanup_stale_snapshots_removes_stale_rollouts` | 清理过期快照时移除过期的 rollout |
| `cleanup_stale_snapshots_skips_active_session` | 清理过期快照时跳过活跃会话 |
