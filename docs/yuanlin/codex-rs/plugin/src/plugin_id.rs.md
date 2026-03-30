# `plugin_id.rs` — 插件 ID 解析与验证

## 功能概述

提供稳定的插件标识符解析和验证功能。`PluginId` 由 `plugin_name@marketplace_name` 格式组成，每个段只允许 ASCII 字母数字、`-` 和 `_`。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `PluginId` | struct | 插件标识符：plugin_name + marketplace_name |
| `PluginIdError` | enum | 解析错误 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `parse` | `(plugin_key) -> Result<Self>` | 从 `name@marketplace` 字符串解析 |
| `validate_plugin_segment` | `(segment, kind) -> Result<()>` | 验证单个段的合法性 |

## 依赖关系

- **被引用方**: `plugin/src/lib.rs`
