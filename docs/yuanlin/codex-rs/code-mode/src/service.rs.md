# `service.rs` — Code Mode 服务层与会话管理

## 功能概述
该文件实现了 `CodeModeService`，这是 code-mode 功能的核心服务层。它管理 V8 运行时会话的生命周期，协调代码执行请求与工具调用的双向通信，支持执行超时让出（yield）、轮询等待和终止操作。服务还包含一个 turn worker 用于异步处理工具调用和通知分发。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `CodeModeService` | struct | 核心服务，管理会话和存储值 |
| `CodeModeTurnHost` | trait | 工具调用和通知的宿主接口 |
| `CodeModeTurnWorker` | struct | 异步 turn worker，处理工具调用分发 |
| `SessionControlCommand` | enum | 会话控制命令（Poll/Terminate） |
| `PendingResult` | struct | 缓存的执行结果 |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `execute` | `(&self, request) -> Result<RuntimeResponse, String>` | 执行代码并返回运行时响应 |
| `wait` | `(&self, request) -> Result<RuntimeResponse, String>` | 等待或终止现有会话 |
| `start_turn_worker` | `(&self, host) -> CodeModeTurnWorker` | 启动异步工具调用分发 worker |
| `run_session_control` | `(inner, context, ...) async` | 核心会话控制循环 |

## 依赖关系
- **引用的 crate**: `tokio`, `async_trait`, `serde_json`, `tokio_util`
- **被引用方**: codex-core 中的 code-mode 功能入口

## 实现备注
- 使用 `tokio::select!` 同时监听运行时事件、控制命令和超时定时器
- 支持 yield 机制让长时间运行的代码定期释放控制权
- 测试模块覆盖了同步退出、console 隔离、输出辅助函数和终止等待等场景
