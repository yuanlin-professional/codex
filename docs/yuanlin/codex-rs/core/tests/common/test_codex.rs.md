# `test_codex.rs` — Codex 集成测试构建器与运行时

## 功能概述

提供 Core 集成测试的核心基础设施：`TestCodexBuilder`（构建器）和 `TestCodex`（测试运行时实例）。通过 Builder 模式，测试可以灵活配置模型提供商、认证方式、shell 覆盖、配置修改器等参数，然后一键构建出一个隔离的 `CodexThread` 实例，并提供 `submit_turn()` 等便捷方法来驱动完整的 turn 执行-等待流程。还提供 `TestCodexHarness` 作为更高层的封装，集成了 mock server 和请求体检查功能。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `TestCodexBuilder` | struct | 测试 Codex 实例的构建器，支持链式调用配置 `config_mutators`、`auth`、`pre_build_hooks`、`home`、`user_shell_override` |
| `TestCodex` | struct | 已构建的测试 Codex 实例，持有 `home`、`cwd`、`codex`（`Arc<CodexThread>`）、`session_configured`、`config`、`thread_manager` |
| `TestCodexHarness` | struct | 更高层封装，内含 `MockServer` 和 `TestCodex`，提供 `submit()`、`function_call_stdout()` 等便捷方法 |
| `TestEnv` | struct | 测试环境抽象，支持本地和远程（Docker 容器内的 exec-server）两种模式 |
| `ApplyPatchModelOutput` | enum | `apply_patch` 调用的不同输出格式：`Freeform`、`Function`、`Shell`、`ShellViaHeredoc`、`ShellCommandViaHeredoc` |
| `ShellModelOutput` | enum | shell 调用的不同输出格式：`Shell`、`ShellCommand`、`LocalShell` |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `test_codex()` | `fn() -> TestCodexBuilder` | 创建默认的 TestCodexBuilder（使用 dummy API key） |
| `TestCodexBuilder::build` | `async fn(&mut self, server: &MockServer) -> Result<TestCodex>` | 使用 wiremock 服务器构建测试实例 |
| `TestCodexBuilder::build_with_websocket_server` | `async fn(&mut self, server: &WebSocketTestServer) -> Result<TestCodex>` | 使用 WebSocket 测试服务器构建实例 |
| `TestCodexBuilder::build_remote_aware` | `async fn(&mut self, server: &MockServer) -> Result<TestCodex>` | 构建支持远程执行环境的测试实例 |
| `TestCodexBuilder::resume` | `async fn(&mut self, server, home, rollout_path) -> Result<TestCodex>` | 从 rollout 文件恢复会话 |
| `TestCodex::submit_turn` | `async fn(&self, prompt: &str) -> Result<()>` | 提交 user turn 并等待 turn 完成 |
| `TestCodex::submit_turn_with_policies` | `async fn(&self, prompt, approval, sandbox) -> Result<()>` | 带策略参数的 turn 提交 |
| `TestCodexHarness::new` | `async fn() -> Result<Self>` | 创建包含 mock server 的完整测试 harness |

## 依赖关系

- **引用的 crate**: `codex_core`、`codex_exec_server`、`codex_protocol`、`codex_features`、`codex_utils_cargo_bin`、`wiremock`、`tempfile`
- **被引用方**: 被几乎所有 Core 集成测试通过 `test_codex()` 构建测试实例

## 实现备注

- `prepare_config()` 自动设置 `codex_self_exe` 路径（尝试 `codex` -> `codex-exec` -> 当前测试二进制的同级目录）
- `ensure_test_model_catalog()` 对特殊的 `test-gpt-5.1-codex` 模型自动构建包含 `experimental_supported_tools` 的 catalog
- 远程测试环境通过 `docker cp` 将 `codex-exec-server` 拷贝到容器中启动，使用 WebSocket 连接通信
- `RemoteExecServerProcess` 的 `Drop` 实现自动清理远程容器中的进程和临时文件
