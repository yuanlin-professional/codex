# `frame_requester.rs` — 帧绘制调度工具

## 功能概述

本模块提供 `FrameRequester`，一个轻量级句柄，允许 widget 和后台任务请求 TUI 的未来重绘。内部生成一个 `FrameScheduler` actor 任务，将多个请求合并为 broadcast channel 上的单次通知。采用 "Actors with Tokio" 设计模式，具有专用的调度任务和轻量级请求句柄。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `FrameRequester` | struct | 帧绘制请求句柄（可 Clone，跨任务共享） |
| `FrameScheduler` | struct | 帧调度 actor（内部使用） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `schedule_frame` | `(&self)` | 尽快安排一次帧绘制 |
| `schedule_frame_in` | `(&self, dur)` | 在指定延迟后安排帧绘制 |
| `test_dummy` | `() -> Self` | 创建测试用的 no-op 请求器 |

## 依赖关系

- **引用的 crate**: `tokio::sync`, `super::frame_rate_limiter`
- **被引用方**: 几乎所有 TUI widget 和动画组件

## 实现备注

- 调度器在异步循环中运行，通过 `tokio::select!` 合并请求和超时
- 多个立即请求合并为单次绘制
- 延迟请求也参与合并，取最早的截止时间
- 通过 `FrameRateLimiter` 限制最高 120 FPS
- 包含 8 个测试验证合并、延迟和帧率限制行为
