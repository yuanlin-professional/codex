# `app_command.rs` — 应用命令封装与视图模式

## 功能概述

本文件定义了 `AppCommand` 结构体及其关联的 `AppCommandView` 枚举，作为 TUI 向核心协议层发送操作指令的统一封装。`AppCommand` 内部包装了 `codex_protocol::protocol::Op`，提供类型安全的构造方法和借用视图（view pattern），使得调用方可以在不暴露底层 `Op` 细节的情况下构建和检查命令。

`AppCommandView` 是一个借用枚举，允许对已有的 `AppCommand` 进行模式匹配而无需克隆，涵盖了所有支持的操作类型：中断、用户轮次、审批决策、配置重载、线程回滚、代码审查等。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `AppCommand` | 结构体（newtype） | 对 `Op` 的封装，提供命令构造和转换方法 |
| `AppCommandView<'a>` | 枚举 | 对 `AppCommand` 内部 `Op` 的借用视图，支持无拷贝模式匹配 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `interrupt()` | `fn() -> Self` | 构造中断命令 |
| `user_turn(...)` | `fn(...) -> Self` | 构造用户轮次命令（含模型、策略、协作模式等参数） |
| `override_turn_context(...)` | `fn(...) -> Self` | 构造轮次上下文覆盖命令 |
| `exec_approval(...)` | `fn(id, turn_id, decision) -> Self` | 构造执行审批命令 |
| `thread_rollback(num_turns)` | `fn(u32) -> Self` | 构造线程回滚命令 |
| `view()` | `fn(&self) -> AppCommandView<'_>` | 获取命令的借用视图用于模式匹配 |
| `into_core(self)` | `fn(self) -> Op` | 消费命令并返回内部 `Op` |

## 依赖关系

- **引用的 crate**: `codex_protocol`（Op、各种协议类型）、`codex_core::config::types`、`serde`
- **被引用方**: `chatwidget.rs`（通过 `submit_op` 发送命令）、`app_event_sender.rs`（包装命令为事件）、`app.rs`

## 实现备注

提供了 `From<Op>`、`From<&Op>`、`From<&AppCommand>` 等多种转换 trait 实现，方便在不同抽象层级之间传递命令。`view()` 方法使用了详尽的模式匹配，确保新增 `Op` 变体时编译器会强制更新。
