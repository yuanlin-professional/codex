# `linux_inhibitor.rs` — Linux 睡眠抑制器后端

## 功能概述

Linux 平台的睡眠抑制器实现，通过生成 `systemd-inhibit` 或 `gnome-session-inhibit` 子进程来阻止系统空闲睡眠。使用"阻塞器"模式：子进程运行 `sleep 2147483647`（i32::MAX 秒）保持活动。支持后端自动降级：优先使用上次成功的后端，失败时尝试另一个。通过 `PR_SET_PDEATHSIG` 确保父进程退出时子进程被终止。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `LinuxSleepInhibitor` | struct | Linux 睡眠抑制器 |
| `InhibitState` | enum | 状态：`Inactive` 或 `Active { backend, child }` |
| `LinuxBackend` | enum | 后端：`SystemdInhibit`、`GnomeSessionInhibit` |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `acquire` | `fn(&mut self)` | 获取睡眠抑制 |
| `release` | `fn(&mut self)` | 释放睡眠抑制（杀死子进程） |

## 依赖关系

- **引用的 crate**: `libc`, `tracing`
- **被引用方**: `sleep-inhibitor/src/lib.rs`

## 实现备注

- 缺少后端的警告仅记录一次以避免日志刷屏。
- `Drop` 实现确保子进程被清理。
