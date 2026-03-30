# `windows_inhibitor.rs` — Windows 睡眠抑制器后端

## 功能概述

Windows 平台的睡眠抑制器实现，使用 `PowerCreateRequest`/`PowerSetRequest` API 配合 `PowerRequestSystemRequired` 请求类型来防止系统空闲睡眠（不强制保持显示器亮起）。`PowerRequest` 结构体通过 RAII 在 `Drop` 时调用 `PowerClearRequest` 和 `CloseHandle` 释放资源。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `WindowsSleepInhibitor` | struct | Windows 睡眠抑制器 |
| `PowerRequest` | struct(private) | Windows 电源请求 RAII 封装 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `acquire` | `fn(&mut self)` | 创建电源请求 |
| `release` | `fn(&mut self)` | 释放电源请求 |

## 依赖关系

- **引用的 crate**: `windows_sys`, `tracing`
- **被引用方**: `sleep-inhibitor/src/lib.rs`

## 实现备注

- 使用 `REASON_CONTEXT_SIMPLE_STRING` 风格的上下文。
- 创建失败和释放失败均记录警告但不 panic。
