# `controller.rs` — 流式输出控制器

## 功能概述

管理换行符门控的流式输出、头部发射和 commit 动画的高层控制器。`StreamController` 管理消息流（agent 消息），`PlanStreamController` 管理计划流。控制器从 markdown 收集器中获取提交的行，按照策略（基线或追赶）排出到历史记录，并在流完成时执行终结处理。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `StreamController` | struct | 消息流控制器，管理 StreamState 和头部发射 |
| `PlanStreamController` | struct | 计划流控制器，使用提议计划样式渲染 |

## 依赖关系

- **引用的 crate**: `crate::history_cell`, `crate::style`
- **被引用方**: `streaming::commit_tick`, `ChatWidget`

## 实现备注

- 控制器在创建时快照会话 cwd，后续渲染使用相同路径
- `finishing_after_drain` 标志控制流结束时的最终排出
