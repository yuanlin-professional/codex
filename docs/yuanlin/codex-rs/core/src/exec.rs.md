# `exec.rs` — 命令执行引擎：进程沙箱化与输出捕获

## 功能概述

`exec.rs`（约 1235 行）实现了 Codex 的命令执行基础设施，负责在沙箱环境中运行 shell 命令并捕获输出。它支持多种沙箱类型（Linux Landlock/Seccomp、macOS Seatbelt、Windows Restricted Token/Elevated）、可配置的超时/取消机制、输出字节上限、实时输出 delta 事件流以及聚合输出合并。文件还包含 Windows 特有的沙箱执行路径和错误诊断逻辑。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ExecParams` | struct | 执行参数：command、cwd、expiration、capture_policy、env、network、sandbox_permissions、windows_sandbox_level 等 |
| `ExecExpiration` | enum | 执行过期机制：Timeout(Duration) / DefaultTimeout / Cancellation(CancellationToken) |
| `ExecCapturePolicy` | enum | 捕获策略：ShellTool（有输出上限和超时）/ FullBuffer（无限制） |
| `ExecToolCallOutput` | struct | 执行结果：exit_code、stdout、stderr、aggregated_output、duration、timed_out |
| `StreamOutput<T>` | struct | 流输出：text + truncated_after_lines |
| `RawExecToolCallOutput` | struct | 原始执行结果（字节级） |
| `StdoutStream` | struct | 实时输出流：sub_id、call_id、tx_event |
| `WindowsRestrictedTokenFilesystemOverlay` | struct | Windows 受限令牌文件系统覆盖层 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `process_exec_tool_call` | `async fn process_exec_tool_call(params, policies...) -> Result<ExecToolCallOutput>` | 工具调用的主执行入口 |
| `build_exec_request` | `fn build_exec_request(params, policies...) -> Result<ExecRequest>` | 将通用执行参数转换为具体的沙箱化执行请求 |
| `execute_exec_request` | `async fn execute_exec_request(exec_request, ...) -> Result<ExecToolCallOutput>` | 执行已构建的执行请求 |
| `exec` | `async fn exec(params, sandbox, policies...) -> Result<RawExecToolCallOutput>` | 底层执行：spawn 子进程、消费输出、处理超时 |
| `consume_output` | `async fn consume_output(child, expiration, capture_policy, stdout_stream)` | 消费子进程输出：并行读取 stdout/stderr、超时处理、kill 进程组 |
| `read_output` | `async fn read_output(reader, stream, is_stderr, max_bytes)` | 异步读取单个输出流，发出 delta 事件 |
| `finalize_exec_result` | `fn finalize_exec_result(raw, sandbox, duration)` | 最终化执行结果：检测超时/信号/沙箱拒绝 |
| `is_likely_sandbox_denied` | `fn is_likely_sandbox_denied(sandbox, output) -> bool` | 启发式判断命令是否被沙箱拒绝 |
| `aggregate_output` | `fn aggregate_output(stdout, stderr, max_bytes)` | 聚合 stdout/stderr 输出（1/3 stdout + 2/3 stderr 分配策略） |
| `resolve_windows_restricted_token_filesystem_overlay` | `fn ...` | 解析 Windows 受限令牌文件系统覆盖层 |
| `exec_windows_sandbox` | `async fn ...` | Windows 沙箱执行路径 |

## 依赖关系

- **引用的 crate**: `codex_sandboxing`（沙箱管理器）、`codex_network_proxy`（网络代理）、`codex_protocol`（策略类型）、`codex_utils_pty`（进程组管理）、`codex_utils_absolute_path`、`tokio`
- **被引用方**: `tools/handlers` 中的 ShellHandler 和 UnifiedExecHandler 调用 `process_exec_tool_call`

## 实现备注

1. **输出上限**：ShellTool 模式下 stdout/stderr/聚合输出各有 `DEFAULT_OUTPUT_BYTES_CAP` 限制，防止 OOM。
2. **Delta 事件上限**：每次执行最多发出 `MAX_EXEC_OUTPUT_DELTAS_PER_CALL`(10000) 个 delta 事件。
3. **I/O 排水超时**：子进程终止后等待 2 秒排空 stdout/stderr 管道，防止孙进程持有管道句柄导致挂起。
4. **沙箱拒绝检测**：通过关键词匹配（"operation not permitted"、"read-only file system" 等）和特定退出码（SIGSYS）启发式判断。
5. **聚合输出分配**：当总输出超过上限时，按 1/3 stdout + 2/3 stderr 分配空间，未使用的 stderr 配额回给 stdout。
6. **Windows 双路径**：非提权使用 Restricted Token 沙箱，提权或代理强制时使用 Elevated 沙箱（logon-user 身份）。
