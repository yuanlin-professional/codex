# `env.rs` — 环境检测函数

## 功能概述

提供跨 crate 共享的环境检测函数。`is_wsl` 通过检查 `WSL_DISTRO_NAME` 环境变量或 `/proc/version` 内容判断是否运行在 WSL 下（仅 Linux 有效）。`is_headless_environment` 检测无 GUI 环境（CI、SSH、Linux 下无 DISPLAY/WAYLAND_DISPLAY），用于避免尝试打开浏览器。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `is_wsl` | `fn() -> bool` | 检测 WSL 环境 |
| `is_headless_environment` | `fn() -> bool` | 检测无 GUI 环境 |

## 依赖关系

- **引用的 crate**: `std::env`, `std::fs`
- **被引用方**: `path-utils/src/lib.rs` 和前端认证流程
