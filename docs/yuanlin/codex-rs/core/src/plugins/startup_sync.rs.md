# `startup_sync.rs` -- 启动时的插件仓库同步

## 功能概述
该文件实现了 Codex 启动时的精选插件仓库同步机制。支持两种同步方式：Git clone（优先）和 GitHub HTTP API（降级方案）。同步流程包括获取远程 SHA、与本地版本比较、下载/克隆最新代码、解压并激活。此外还实现了启动时的远程插件状态一次性同步（additive_only 模式）。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `GitHubRepositorySummary` | struct (private) | GitHub 仓库信息，包含默认分支名 |
| `GitHubGitRefSummary` | struct (private) | GitHub Git 引用信息，包含 SHA 对象 |
| `GitHubGitRefObject` | struct (private) | Git 引用对象，包含 SHA 值 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `curated_plugins_repo_path` | `fn(codex_home: &Path) -> PathBuf` | 获取精选插件仓库的本地路径（`.tmp/plugins`） |
| `read_curated_plugins_sha` | `fn(codex_home: &Path) -> Option<String>` | 读取本地精选插件的 SHA 版本文件 |
| `sync_openai_plugins_repo` | `fn(codex_home: &Path) -> Result<String, String>` | 同步 OpenAI 插件仓库（先尝试 Git，失败后降级到 HTTP） |
| `sync_openai_plugins_repo_via_git` | `fn(codex_home, git_binary) -> Result<String>` | 通过 Git 克隆同步精选仓库 |
| `sync_openai_plugins_repo_via_http` | `fn(codex_home, api_base_url) -> Result<String>` | 通过 GitHub API 下载 ZIP 包同步 |
| `start_startup_remote_plugin_sync_once` | `fn(manager, codex_home, config, auth_manager)` | 启动一次性的远程插件状态同步任务 |
| `activate_curated_repo` | `fn(repo_path, staged_repo_dir) -> Result<()>` | 原子性地激活新仓库（带备份和回滚机制） |
| `extract_zipball_to_dir` | `fn(bytes, destination) -> Result<()>` | 解压 ZIP 包到目标目录（去除顶层目录前缀） |
| `run_git_command_with_timeout` | `fn(command, context, timeout) -> Result<Output>` | 带超时的 Git 命令执行器 |
| `remove_stale_curated_repo_temp_dirs` | `fn(parent, max_age)` | 清理过期的临时克隆目录 |

## 依赖关系
- **引用的 crate**: `reqwest`, `tempfile`, `zip`, `tracing`, `codex_otel`
- **引用的内部模块**: `crate::default_client`, `crate::config::Config`, `crate::AuthManager`, `super::PluginsManager`
- **被引用方**: `manager.rs` 调用同步函数；`mod.rs` 导出 `curated_plugins_repo_path`、`read_curated_plugins_sha`、`sync_openai_plugins_repo`

## 实现备注
- Git 同步使用 `--depth 1` 浅克隆以减少下载量
- Git 命令超时为 30 秒，HTTP 请求也为 30 秒
- 使用原子性的目录交换（rename）激活新仓库，失败时自动回滚
- 过期的临时克隆目录（超过 10 分钟）会被自动清理
- ZIP 解压时正确剥离 GitHub zipball 的顶层目录前缀
- Unix 平台上会保留 ZIP 中的文件权限
- 远程插件同步使用 marker 文件防止重复执行，marker 路径为 `.tmp/app-server-remote-plugin-sync-v1`
- 远程同步前会等待精选市场准备就绪（最多 5 秒超时）
- 发射 OpenTelemetry 指标记录同步的传输方式和结果
