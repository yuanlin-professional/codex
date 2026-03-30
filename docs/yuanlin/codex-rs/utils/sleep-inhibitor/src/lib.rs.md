# `lib.rs` — 跨平台睡眠抑制器

## 功能概述

提供跨平台的空闲睡眠防止功能，在 turn 运行期间保持机器唤醒。平台行为：macOS 使用 IOKit 电源断言，Linux 使用 `systemd-inhibit` 或 `gnome-session-inhibit` 子进程，Windows 使用 `PowerCreateRequest`/`PowerSetRequest`，其他平台为空操作。`SleepInhibitor` 封装了启用状态和 turn 运行状态的切换逻辑。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `SleepInhibitor` | struct | 睡眠抑制器，包含 enabled、turn_running 和平台后端 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `fn(enabled: bool) -> Self` | 创建睡眠抑制器 |
| `set_turn_running` | `fn(&mut self, turn_running: bool)` | 更新 turn 运行状态并切换睡眠防止 |
| `is_turn_running` | `fn(&self) -> bool` | 返回当前 turn 运行状态 |

## 依赖关系

- **引用的 crate**: 平台特定模块
- **被引用方**: 核心执行引擎

## 实现备注

- `enabled` 为 `false` 时始终 release，不会 acquire。
- 测试验证了启用/禁用、多次切换和幂等性。
