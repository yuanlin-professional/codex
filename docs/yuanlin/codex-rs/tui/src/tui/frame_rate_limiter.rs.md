# `frame_rate_limiter.rs` — 帧率限制器

## 功能概述

限制帧绘制通知的最大频率为 120 FPS（约 8.33ms 最小间隔）。Widget 有时会比用户可感知的频率更频繁地调用 `schedule_frame()`，此限制器将绘制通知钳制到最大帧率以避免浪费工作。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `FrameRateLimiter` | struct | 帧率限制器，记录最近一次发射时间 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `clamp_deadline` | `(&self, requested) -> Instant` | 将请求时间钳制到不超过最大帧率 |
| `mark_emitted` | `(&mut self, at)` | 记录一次帧发射 |

## 依赖关系

- **被引用方**: `FrameScheduler`
