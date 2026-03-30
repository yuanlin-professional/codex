# `ascii_animation.rs` — ASCII 艺术动画驱动器

## 功能概述

本文件实现了 `AsciiAnimation` 结构体，负责驱动 TUI 中弹出窗口和引导界面共享的 ASCII 艺术动画。它管理动画变体集合、帧率控制和帧调度，支持基于时间的帧索引计算和随机变体切换。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `AsciiAnimation` | 结构体 | 动画驱动器，持有帧请求器、变体列表、当前变体索引、帧间隔和起始时间 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn(request_frame: FrameRequester) -> Self` | 使用所有默认变体创建动画 |
| `with_variants` | `fn(request_frame, variants, variant_idx) -> Self` | 使用指定变体集合创建动画 |
| `schedule_next_frame` | `fn(&self)` | 计算并调度下一帧的绘制时机 |
| `current_frame` | `fn(&self) -> &'static str` | 根据经过时间和帧率返回当前应显示的帧 |
| `pick_random_variant` | `fn(&mut self) -> bool` | 随机切换到不同的动画变体 |

## 依赖关系

- **引用的 crate**: `rand`、`crate::frames`（动画帧数据）、`crate::tui::FrameRequester`
- **被引用方**: 引导界面（onboarding）、等待/加载弹窗

## 实现备注

帧索引通过 `elapsed_ms / tick_ms % frame_count` 计算，确保动画循环播放。下一帧调度使用精确的延迟计算，避免累积漂移。随机变体切换确保不会选中当前变体。
