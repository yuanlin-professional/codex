# `lib.rs` — Core 测试公共支持库入口

## 功能概述

`lib.rs` 是 `core_test_support` crate 的入口文件，提供了 Codex Core 集成测试所需的通用工具函数、路径辅助方法、宏定义以及模块导出。它在进程初始化阶段（通过 `ctor`）设置确定性进程 ID、arg0 分发和 insta 快照工作区根目录，确保所有测试的运行环境一致且隔离。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `RemoteEnvConfig` | struct | 远程测试环境配置，持有 `container_name` 字段 |
| `PathExt` / `PathBufExt` / `TempDirExt` | trait | 为 `Path`/`PathBuf`/`TempDir` 提供 `.abs()` 便捷方法将路径转为 `AbsolutePathBuf` |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `load_default_config_for_test` | `async fn(codex_home: &TempDir) -> Config` | 使用临时目录作为 CODEX_HOME 构建隔离的默认测试配置 |
| `assert_regex_match` | `fn(pattern: &str, actual: &str) -> Captures` | 正则断言匹配辅助，匹配失败时 panic 并输出清晰信息 |
| `test_path_buf` / `test_absolute_path` | `fn(unix_path: &str) -> PathBuf/AbsolutePathBuf` | 跨平台路径构建工具，Windows 下自动转换路径格式 |
| `load_sse_fixture` | `fn(path: impl AsRef<Path>) -> String` | 从 JSON fixture 文件构建 SSE 流文本 |
| `wait_for_event` | `async fn(codex: &CodexThread, predicate: F) -> EventMsg` | 异步等待匹配谓词的事件，带超时 |
| `format_with_current_shell` | `fn(command: &str) -> Vec<String>` | 获取当前 shell 的命令执行参数格式 |
| `get_remote_test_env` | `fn() -> Option<RemoteEnvConfig>` | 读取 `CODEX_TEST_REMOTE_ENV` 环境变量获取远程测试环境配置 |
| `fetch_dotslash_file` | `fn(dotslash_file: &Path, ...) -> Result<PathBuf>` | 通过 DotSlash 工具获取资源文件的实际路径 |

## 依赖关系

- **引用的 crate**: `codex_core`、`codex_arg0`、`codex_utils_cargo_bin`、`codex_utils_absolute_path`、`regex_lite`、`tempfile`、`notify`、`walkdir`、`ctor`
- **被引用方**: 被所有 `codex-core` 集成测试作为公共依赖使用

## 实现备注

- 通过 `#[ctor]` 在进程启动时设置 `set_thread_manager_test_mode(true)` 和 `set_deterministic_process_ids(true)`，保证测试确定性
- `fs_wait` 子模块提供基于文件系统 watcher 的异步等待功能（`wait_for_path_exists`、`wait_for_matching_file`），用于等待异步创建的文件
- 提供 `skip_if_sandbox!`、`skip_if_no_network!`、`codex_linux_sandbox_exe_or_skip!`、`skip_if_windows!` 等条件跳过宏
- Linux 平台特有的 `find_codex_linux_sandbox_exe` 用于定位沙箱可执行文件
