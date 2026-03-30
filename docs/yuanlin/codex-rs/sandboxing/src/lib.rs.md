# `lib.rs` — sandboxing 库入口与平台适配

## 功能概述

作为 `codex_sandboxing` crate 的入口文件，根据目标平台条件编译不同的沙箱模块。Linux 上导出 `bwrap` 模块，macOS 上导出 `seatbelt` 模块，所有平台都导出 `landlock`、`policy_transforms` 和 `manager` 模块。统一导出核心类型（SandboxManager、SandboxCommand、SandboxType 等），并为非 Linux 平台提供 `system_bwrap_warning` 的空实现。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| (公开导出) | 各种类型 | SandboxCommand、SandboxExecRequest、SandboxManager、SandboxTransformError、SandboxTransformRequest、SandboxType、SandboxablePreference、get_platform_sandbox |

## 依赖关系

- **被引用方**: Codex runtime 层

## 实现备注

- 模块条件编译：`bwrap`（仅 Linux）、`seatbelt`（仅 macOS）、`landlock`/`manager`/`policy_transforms`（所有平台）。
- 非 Linux 平台上 `system_bwrap_warning()` 始终返回 `None`。
