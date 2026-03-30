# `request_permissions.rs` — 权限请求协议类型

## 功能概述

定义了 Agent 在运行时向用户请求额外权限的协议类型，包括权限授予范围（Turn 级别或 Session 级别）、请求的权限配置文件、请求参数、响应及事件。在 Agent 发现当前权限不足以完成任务时，会通过此机制向用户请求网络或文件系统的额外权限。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `PermissionGrantScope` | 枚举 | 权限授予范围（Turn / Session） |
| `RequestPermissionProfile` | 结构体 | 请求的权限配置（网络权限 + 文件系统权限） |
| `RequestPermissionsArgs` | 结构体 | 权限请求工具参数（原因 + 权限配置） |
| `RequestPermissionsResponse` | 结构体 | 权限请求响应（授予的权限 + 范围） |
| `RequestPermissionsEvent` | 结构体 | 权限请求事件（call_id + turn_id + 原因 + 权限） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `RequestPermissionProfile::is_empty` | `fn is_empty(&self) -> bool` | 判断权限配置是否为空 |

## 依赖关系

- **引用的 crate**: `schemars`, `serde`, `ts_rs`
- **被引用方**: `protocol.rs` 导出 `RequestPermissionsArgs` 和 `RequestPermissionsEvent`

## 实现备注

- `RequestPermissionProfile` 与 `PermissionProfile` 之间提供了双向 `From` 转换。
- `PermissionGrantScope` 默认为 `Turn`（仅当前轮次生效）。
