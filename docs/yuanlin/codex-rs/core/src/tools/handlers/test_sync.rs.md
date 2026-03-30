# `test_sync.rs` — 测试同步工具处理器

## 功能概述
该文件实现了一个专用于测试的同步原语工具 `test_sync_tool`，提供了可配置的延迟（sleep_before/sleep_after）和命名屏障（barrier）机制。此工具用于在集成测试中协调多个并发工具调用的执行时序，确保特定操作以可预测的顺序发生。屏障机制基于 Tokio 的 `Barrier`，支持超时和参与者数量验证。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `TestSyncHandler` | struct | 实现 `ToolHandler` trait 的测试同步处理器 |
| `TestSyncArgs` | struct | 工具参数：可选的前/后延迟和屏障配置 |
| `BarrierArgs` | struct | 屏障参数：id、参与者数、超时毫秒数 |
| `BarrierState` | struct | 屏障状态：包含 `Arc<Barrier>` 和参与者数 |
| `BARRIERS` | static (OnceLock) | 全局屏障注册表，使用 Tokio Mutex 保护的 HashMap |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `handle` | `async fn handle(&self, invocation) -> Result<FunctionToolOutput, FunctionCallError>` | 按顺序执行：前延迟 -> 屏障等待 -> 后延迟，返回 "ok" |
| `wait_on_barrier` | `async fn wait_on_barrier(args: BarrierArgs) -> Result<(), FunctionCallError>` | 管理屏障生命周期：创建/复用屏障，带超时等待，leader 负责清理 |
| `barrier_map` | `fn barrier_map() -> &'static tokio::sync::Mutex<HashMap<...>>` | 获取全局屏障注册表 |

## 依赖关系
- **引用的 crate**: `async_trait`, `serde`, `tokio`（Barrier, Mutex, sleep, timeout）
- **内部依赖**: `tools::context`, `tools::registry`, `tools::handlers::parse_arguments`
- **被引用方**: 仅在测试场景中使用

## 实现备注
- 屏障注册表是进程级全局的（`OnceLock`），允许跨测试用例的工具调用共享屏障
- 同一 barrier id 的参与者数必须一致，否则报错
- leader（最后一个到达屏障的参与者）负责从全局 map 中清理已完成的屏障
- 使用 `Arc::ptr_eq` 确保仅清理当前批次的屏障实例，避免竞态条件
