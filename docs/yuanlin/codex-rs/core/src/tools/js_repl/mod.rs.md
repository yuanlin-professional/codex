# `mod.rs` — JavaScript REPL 模块

## 功能概述
实现了一个完整的 JavaScript REPL（Read-Eval-Print Loop）运行时，通过 Node.js 子进程提供 JavaScript 代码执行能力。该模块管理 Node.js 内核的生命周期（启动、通信、重置、终止），支持嵌套工具调用（脚本中调用 Codex 工具）、图像发射、超时控制以及详细的诊断日志。这是 Codex 中 `js_repl` 工具的核心实现，行数约 1970 行，是整个工具系统中最复杂的模块之一。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `JsReplHandle` | struct | Per-turn 的 REPL 句柄，使用 `OnceCell` 懒初始化 `JsReplManager` |
| `JsReplArgs` | struct | 执行参数：`code`（JavaScript 源码）、`timeout_ms`（可选超时） |
| `JsExecResult` | struct | 执行结果：`output` 文本和 `content_items` 内容项列表 |
| `JsReplManager` | struct | REPL 管理器，管理内核进程、执行锁、工具调用状态 |
| `KernelState` | struct | 内核状态，持有子进程、stdin/stdout、pending 执行映射 |
| `TopLevelExecState` | enum | 顶层执行状态机：Idle/FreshKernel/ReusedKernelPending/Submitted |
| `KernelToHost` / `HostToKernel` | enum | 内核与宿主之间的 JSON 消息协议 |
| `NodeVersion` | struct | Node.js 版本号解析与比较 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `JsReplManager::execute` | `async fn execute(&self, session, turn, tracker, args)` | 主执行入口：获取执行锁、确保内核已启动、提交代码、等待结果 |
| `JsReplManager::start_kernel` | `async fn start_kernel(&self, turn, dependency_env, thread_id)` | 启动 Node.js 子进程，配置沙箱、环境变量，写入内核脚本 |
| `JsReplManager::read_stdout` | `async fn read_stdout(...)` | stdout 读取循环，处理 ExecResult/RunTool/EmitImage 三类消息 |
| `JsReplManager::run_tool_request` | `async fn run_tool_request(exec, req)` | 处理内核发起的嵌套工具调用请求 |
| `JsReplManager::reset` | `async fn reset(&self)` | 重置内核（终止进程、清理状态） |
| `resolve_compatible_node` | `async fn resolve_compatible_node(config_path)` | 解析并验证 Node.js 路径和版本 |

## 依赖关系
- **引用的 crate**: `tokio`, `serde`, `uuid`, `tracing`, `tempfile`, `codex_sandboxing`, `codex_protocol`
- **内部依赖**: `crate::tools::ToolRouter`, `crate::exec`, `crate::sandboxing`, `crate::exec_env`
- **被引用方**: `tools/handlers/` 中的 `JsReplHandler` 和 `JsReplResetHandler`

## 实现备注
- **进程通信**: 通过 stdin/stdout 的 JSON 行协议与 Node.js 内核通信
- **嵌套工具调用**: 内核脚本可发起 `RunTool` 请求，宿主端异步执行后通过 `RunToolResult` 返回
- **图像发射**: 支持 `EmitImage` 请求，仅接受 data URL 格式
- **stderr 监控**: 使用环形缓冲区（`VecDeque`）记录最近 stderr 输出，用于诊断
- **执行锁**: 使用 `Semaphore(1)` 确保同一时间只有一个执行请求
- **超时处理**: 超时后自动重置内核并返回错误信息
- **最小 Node 版本**: 从 `node-version.txt` 读取，启动时验证
