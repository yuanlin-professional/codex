# `plugin_namespace.rs` — 插件命名空间解析

## 功能概述

通过遍历文件路径的祖先目录查找 `plugin.json` 清单文件，解析插件命名空间名称。用于将技能文件路径关联到其所属的插件。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `plugin_namespace_for_skill_path` | `(path) -> Option<String>` | 查找最近的祖先目录中的插件清单名称 |

## 依赖关系

- **被引用方**: `plugin/src/lib.rs`（通过 re-export）
