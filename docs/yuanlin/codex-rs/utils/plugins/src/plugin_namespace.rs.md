# `plugin_namespace.rs` — 插件命名空间解析

## 功能概述

通过遍历给定技能文件路径的祖先目录，查找含有 `plugin.json` 清单文件的插件根目录，并从中提取插件命名空间名称。清单中的 `name` 字段为空时回退到目录名。与 `codex-core` 中完整清单加载使用相同的命名规则。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `plugin_namespace_for_skill_path` | `fn(path: &Path) -> Option<String>` | 从技能路径查找最近祖先的插件命名空间 |

## 依赖关系

- **引用的 crate**: `serde`, `serde_json`, `std::fs`
- **被引用方**: 插件加载和技能解析模块

## 实现备注

- `PLUGIN_MANIFEST_PATH` = `.codex-plugin/plugin.json`。
- 测试验证了从技能路径正确解析到清单中的 `name` 字段。
