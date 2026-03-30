# `test_codex_exec.rs` — codex-exec CLI 测试辅助

## 功能概述

提供 `codex-exec` 命令行工具集成测试的辅助构建器。`TestCodexExecBuilder` 封装了临时 home 和 cwd 目录，提供便捷方法构建预配置的 `assert_cmd::Command` 实例，支持直接运行或与 wiremock `MockServer` 配合使用。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `TestCodexExecBuilder` | struct | 持有 `home: TempDir` 和 `cwd: TempDir`，提供 `cmd()` 和 `cmd_with_server()` 方法 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `test_codex_exec()` | `fn() -> TestCodexExecBuilder` | 创建默认构建器（自动创建临时 home 和 cwd） |
| `TestCodexExecBuilder::cmd` | `fn(&self) -> assert_cmd::Command` | 构建预设了 `CODEX_HOME`、`CODEX_API_KEY_ENV_VAR` 和 cwd 的 codex-exec 命令 |
| `TestCodexExecBuilder::cmd_with_server` | `fn(&self, server: &MockServer) -> assert_cmd::Command` | 在 `cmd()` 基础上追加 `-c openai_base_url=...` 参数指向 mock server |
| `TestCodexExecBuilder::cwd_path` / `home_path` | `fn(&self) -> &Path` | 获取临时目录路径 |

## 依赖关系

- **引用的 crate**: `assert_cmd`、`codex_core::auth`、`codex_utils_cargo_bin`、`tempfile`、`wiremock`
- **被引用方**: 被 `codex-exec` CLI 端到端集成测试使用
