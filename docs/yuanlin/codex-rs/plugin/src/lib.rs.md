# `lib.rs` — 插件标识与遥测摘要

## 功能概述

定义共享的插件标识符、遥测元数据和能力摘要类型。作为插件系统的核心类型定义 crate，导出 `PluginId`、`LoadedPlugin`、`PluginLoadOutcome` 等类型，以及 `PLUGIN_MANIFEST_PATH` 和 `mention_syntax` 等工具函数。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `AppConnectorId` | struct | 应用连接器 ID 包装 |
| `PluginCapabilitySummary` | struct | 插件能力摘要：名称、描述、技能、MCP 服务器、连接器 |
| `PluginTelemetryMetadata` | struct | 插件遥测元数据 |

## 依赖关系

- **引用的 crate**: `codex_utils_plugins`
- **被引用方**: core-skills、app-server 插件加载模块
