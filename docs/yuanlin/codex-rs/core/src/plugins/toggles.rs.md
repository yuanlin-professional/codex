# `toggles.rs` -- 插件启用状态变更提取

## 功能概述
该文件实现了从配置写入操作中提取插件启用/禁用状态变更候选的功能。支持三种配置写入格式：直接设置 `plugins.<id>.enabled`、设置整个插件表 `plugins.<id>`、以及批量设置 `plugins` 根表。用于在配置变更时确定哪些插件的启用状态发生了变化。

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `collect_plugin_enabled_candidates` | `fn(edits: impl Iterator<Item = (&String, &JsonValue)>) -> BTreeMap<String, bool>` | 从配置编辑操作中收集插件启用状态变更。支持 `plugins.id.enabled`、`plugins.id` 和 `plugins` 三种键路径格式。同一插件的多次写入以最后一次为准 |

## 依赖关系
- **引用的 crate**: `serde_json`
- **被引用方**: `mod.rs` 通过 `pub use` 导出

## 实现备注
- 使用 `BTreeMap` 保证输出按键排序
- 三种路径格式通过模式匹配处理：
  - `plugins.<plugin_id>.enabled` -> 直接读取布尔值
  - `plugins.<plugin_id>` -> 从对象中提取 `enabled` 字段
  - `plugins` -> 遍历对象中所有插件条目
- 内嵌了两个单元测试，验证多格式混合写入和相同插件的覆盖行为
