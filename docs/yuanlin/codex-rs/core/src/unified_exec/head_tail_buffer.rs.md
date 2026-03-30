# `head_tail_buffer.rs` — 头尾保留缓冲区实现

## 功能概述

实现了一个具有容量上限的字节缓冲区 `HeadTailBuffer`，在超出最大容量时保留头部前缀和尾部后缀，丢弃中间部分的数据。缓冲区容量对半分配：50% 给头部，50% 给尾部。这种设计确保了在大量输出中，用户可以看到命令执行的起始输出和最终输出，而中间部分被截断。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `HeadTailBuffer` | 结构体 | 容量受限的头尾保留缓冲区，内部使用 `VecDeque<Vec<u8>>` 存储头部和尾部的数据块 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn(max_bytes: usize) -> Self` | 创建指定最大字节数的缓冲区，头尾各占一半预算 |
| `retained_bytes` | `fn(&self) -> usize` | 返回缓冲区当前保留的总字节数（头 + 尾） |
| `omitted_bytes` | `fn(&self) -> usize` | 返回因容量限制而被丢弃的中间字节数 |
| `push_chunk` | `fn(&mut self, chunk: Vec<u8>)` | 追加数据块：优先填充头部预算，超出后填充尾部，旧的尾部数据被淘汰 |
| `snapshot_chunks` | `fn(&self) -> Vec<Vec<u8>>` | 返回当前保留内容的快照（先头后尾） |
| `to_bytes` | `fn(&self) -> Vec<u8>` | 将保留内容拼接为单个字节向量 |
| `drain_chunks` | `fn(&mut self) -> Vec<Vec<u8>>` | 排空所有保留的数据块并重置状态 |
| `push_to_tail` | `fn(&mut self, chunk: Vec<u8>)` | 向尾部追加数据，必要时裁剪以满足尾部预算 |
| `trim_tail_to_budget` | `fn(&mut self)` | 修剪尾部使其不超过预算，从前端淘汰最旧的数据块 |

## 依赖关系

- **引用的 crate**: `std::collections::VecDeque`
- **引用的内部模块**: `crate::unified_exec::UNIFIED_EXEC_OUTPUT_MAX_BYTES`（默认容量 1 MiB）
- **被引用方**: `async_watcher.rs`（作为输出转录缓冲区）、`process.rs`（作为输出缓冲区）、`process_manager.rs`（创建转录实例）

## 实现备注

- 采用对称分配策略：头部和尾部各占 `max_bytes / 2` 的预算。
- 当单个数据块大于整个尾部预算时，只保留该块的最后 `tail_budget` 个字节。
- 尾部修剪从前端（最旧的数据）开始淘汰，可能会部分截断一个数据块（通过 `drain` 操作）。
- `Default` 实现使用 `UNIFIED_EXEC_OUTPUT_MAX_BYTES`（1 MiB）作为默认容量。
