# `startup_sync_tests.rs` -- 测试模块

## 测试覆盖范围
测试 `startup_sync.rs` 中精选插件仓库同步的各种场景，包括 Git 同步、HTTP 降级、ZIP 解压、SHA 匹配跳过、错误处理和远程同步集成。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `curated_plugins_repo_path_uses_codex_home_tmp_dir` | 验证精选仓库路径为 `.tmp/plugins` |
| `read_curated_plugins_sha_reads_trimmed_sha_file` | 验证正确读取并去除 SHA 文件的尾部换行 |
| `remove_stale_curated_repo_temp_dirs_removes_only_matching_directories` | (Unix) 验证只清理过期的 `plugins-clone-` 目录，保留新鲜目录和无关目录 |
| `sync_openai_plugins_repo_prefers_git_when_available` | (Unix) 验证 Git 可用时优先使用 Git 同步 |
| `sync_openai_plugins_repo_falls_back_to_http_when_git_is_unavailable` | 验证 Git 不可用时降级到 HTTP ZIP 下载 |
| `sync_openai_plugins_repo_falls_back_to_http_when_git_sync_fails` | (Unix) 验证 Git 同步失败时降级到 HTTP |
| `sync_openai_plugins_repo_via_git_cleans_up_staged_dir_on_clone_failure` | (Unix) 验证 Git 克隆失败后清理临时目录 |
| `sync_openai_plugins_repo_via_http_cleans_up_staged_dir_on_extract_failure` | 验证 HTTP 下载后解压失败时清理临时目录 |
| `sync_openai_plugins_repo_skips_archive_download_when_sha_matches` | 验证本地 SHA 与远程匹配时跳过下载 |
| `startup_remote_plugin_sync_writes_marker_and_reconciles_state` | 验证启动远程同步写入 marker 文件并正确安装/启用远程插件 |
