# `rate_limits.rs` — 速率限制和额度显示

## 功能概述

将 `RateLimitSnapshot` 协议载荷映射为面向显示的行结构，供 TUI 在 `/status` 和状态栏上下文中渲染，避免重复格式化逻辑。关键契约是时间敏感的值相对于调用者提供的捕获时间戳进行解释，以确保过期检测和重置标签在给定绘制周期内保持一致。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `RateLimitSnapshotDisplay` | struct | 速率限制快照的显示表示 |
| `RateLimitWindowDisplay` | struct | 单个限制窗口的显示数据 |
| `StatusRateLimitRow` | struct | 状态卡片中的速率限制行 |

## 依赖关系

- **引用的 crate**: `codex_protocol::protocol`, `chrono`
- **被引用方**: `status::card`, `chatwidget::status_surfaces`

## 实现备注

- 使用 20 段进度条（█/░）可视化使用比例
- 支持过期数据检测，显示"stale"标记
