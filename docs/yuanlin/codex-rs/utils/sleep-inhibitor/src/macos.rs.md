# `macos.rs` — macOS 睡眠抑制器后端

## 功能概述

macOS 平台的睡眠抑制器实现，使用原生 IOKit 电源断言（`IOPMAssertionCreateWithName`）替代生成 `caffeinate` 进程。创建 `PreventUserIdleSystemSleep` 类型的断言以防止系统空闲睡眠。断言在 `Drop` 时通过 `IOPMAssertionRelease` 自动释放。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `SleepInhibitor` | struct | macOS 睡眠抑制器 |
| `MacSleepAssertion` | struct(private) | IOKit 电源断言 RAII 封装 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `acquire` | `fn(&mut self)` | 创建电源断言 |
| `release` | `fn(&mut self)` | 释放电源断言 |

## 依赖关系

- **引用的 crate**: `core_foundation`, `tracing`
- **被引用方**: `sleep-inhibitor/src/lib.rs`

## 实现备注

- 使用 `core-foundation` 的 `CFString` 和 bindgen 生成的 `CFStringRef` 之间需要指针转换。
- 重复 `acquire` 调用为幂等操作。
