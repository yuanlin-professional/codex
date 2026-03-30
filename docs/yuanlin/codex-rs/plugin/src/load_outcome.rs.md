# `load_outcome.rs` — 插件加载结果

## 功能概述

定义插件加载的输出类型。`LoadedPlugin<M>` 表示从磁盘加载的单个插件（含 MCP 服务器配置、技能根目录、应用连接器等），`PluginLoadOutcome<M>` 聚合所有已加载插件并计算有效的技能根目录、MCP 服务器和应用连接器。包含插件描述的提示安全化处理（截断至 1024 字符）。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `LoadedPlugin<M>` | struct | 已加载的插件：配置名、根路径、MCP 服务器、技能等 |
| `PluginLoadOutcome<M>` | struct | 所有插件的加载结果聚合 |
| `EffectiveSkillRoots` | trait | 获取有效技能根目录的 trait |

## 依赖关系

- **被引用方**: `plugin/src/lib.rs`、core 插件加载模块
