# `mod.rs` — 流式输出原语

## 功能概述

定义 TUI 聊天记录管道中使用的流式输出原语。`StreamState` 拥有换行符门控的 markdown 收集器和已提交渲染行的 FIFO 队列。高层模块在此基础上构建：`controller` 将队列行转化为 HistoryCell 发射规则，`chunking` 从队列压力计算自适应排出计划，`commit_tick` 将策略决策绑定到具体的控制器排出。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `StreamState` | struct | 流式状态，包含 markdown 收集器和行队列 |
| `QueuedLine` | struct | 队列中的行，带入队时间戳 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `step` | `(&mut self) -> Vec<Line>` | 从队列头部排出一行 |
| `drain_n` | `(&mut self, max) -> Vec<Line>` | 排出最多 N 行 |
| `drain_all` | `(&mut self) -> Vec<Line>` | 排出所有行 |
| `enqueue` | `(&mut self, lines)` | 将提交的行加入队列 |
| `oldest_queued_age` | `(&self, now) -> Option<Duration>` | 最旧队列项的年龄 |

## 依赖关系

- **引用的 crate**: `crate::markdown_stream`, `ratatui::text`
- **被引用方**: `streaming::controller`, `streaming::commit_tick`

## 实现备注

- 关键不变量：所有排出从队列前端进行，入队记录到达时间戳
- 策略代码可通过 `oldest_queued_age` 推断最旧条目的年龄
