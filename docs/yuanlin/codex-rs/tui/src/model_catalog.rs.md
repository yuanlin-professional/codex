# `model_catalog.rs` — 模型目录

## 功能概述

封装可用模型列表和协作模式配置。`ModelCatalog` 持有 `ModelPreset` 列表和
`CollaborationModesConfig`，提供查询模型和列出协作模式的接口。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ModelCatalog` | struct | 模型预设列表和协作模式配置的容器 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `(models, config) -> Self` | 创建目录 |
| `try_list_models` | `(&self) -> Result<Vec<ModelPreset>>` | 列出可用模型 |
| `list_collaboration_modes` | `(&self) -> Vec<CollaborationModeMask>` | 列出内置协作模式 |

## 依赖关系

- **引用的 crate**: `codex_core::models_manager`, `codex_protocol`
