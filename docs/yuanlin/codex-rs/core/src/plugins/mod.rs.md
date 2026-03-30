# `mod.rs` -- 插件模块根文件

## 功能概述
这是 `plugins` 模块的根文件，负责声明子模块、定义类型别名，以及统一导出公开和 crate 内部的 API。它是整个插件系统的入口点，将 `manager`、`marketplace`、`manifest`、`store`、`remote`、`render`、`mentions`、`injection`、`discoverable`、`startup_sync`、`toggles` 等子模块的关键类型和函数重导出为统一的公共接口。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `LoadedPlugin` | 类型别名 | `codex_plugin::LoadedPlugin<McpServerConfig>` 的具体化 |
| `PluginLoadOutcome` | 类型别名 | `codex_plugin::PluginLoadOutcome<McpServerConfig>` 的具体化 |

## 依赖关系
- **引用的 crate**: `codex_plugin`（核心类型来源）
- **引用的内部模块**: `crate::config::types::McpServerConfig`
- **被引用方**: 被 crate 中其他模块广泛使用

## 实现备注
- 从 `codex_plugin` crate 重导出 `AppConnectorId`、`EffectiveSkillRoots`、`PluginCapabilitySummary`、`PluginId`、`PluginIdError`、`PluginTelemetryMetadata` 等核心类型
- `test_support` 模块仅在测试配置下编译（`#[cfg(test)]`）
- 统一了来自不同子模块的公开导出，使得外部使用者只需 `use crate::plugins::*` 即可访问所有插件 API
