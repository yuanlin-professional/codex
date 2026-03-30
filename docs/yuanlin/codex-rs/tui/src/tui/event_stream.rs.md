# `event_stream.rs` — TUI 事件流管道

## 功能概述

本模块提供 TUI 事件流的核心管道。`EventBroker` 持有共享的 crossterm 流，多个调用者可复用同一输入源，并支持 drop/recreate 以在暂停/恢复时完全释放 stdin。`TuiEventStream` 包装绘制事件订阅和共享的 EventBroker，将 crossterm 事件映射为 `TuiEvent`。`EventSource` 抽象底层事件生产者，支持测试时替换为 `FakeEventSource`。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `EventBroker` | struct | 共享的 crossterm 事件流持有者 |
| `TuiEventStream` | struct | TUI 事件流，映射 crossterm 事件为 TuiEvent |
| `EventSource` | trait | 事件源抽象（支持测试替换） |

## 依赖关系

- **引用的 crate**: `crossterm::event`, `tokio::sync`
- **被引用方**: `Tui` 主事件循环

## 实现备注

- Drop/recreate crossterm 事件流以完全释放 stdin，避免与子进程（如 vim）竞争输入
- 使用 broadcast channel 分发绘制通知
