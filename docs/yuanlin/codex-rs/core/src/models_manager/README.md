# models_manager/ -- 模型管理器

## 功能概述

`models_manager/` 负责 Codex 的 AI 模型信息管理，包括从 OpenAI API 获取可用模型列表、管理模型元数据缓存、构建模型信息（含基础指令、截断策略、推理能力等），以及管理协作模式预设。它是 Codex 选择和配置正确模型参数的核心组件。

## 目录结构

```
models_manager/
├── mod.rs                               # 模块入口，导出子模块和 client_version_to_whole() 工具函数
├── manager.rs                           # ModelsManager：模型列表获取、缓存刷新、API 请求遥测
├── model_info.rs                        # 模型信息构建：配置覆盖、人格模板、基础指令注入
├── cache.rs                             # ModelsCacheManager：磁盘缓存管理（TTL、版本匹配、加载/保存）
├── model_presets.rs                     # 模型预设配置
├── collaboration_mode_presets.rs        # 协作模式预设（plan/execute/pair_programming 等）
├── manager_tests.rs                     # 管理器测试
├── model_info_tests.rs                  # 模型信息测试
└── collaboration_mode_presets_tests.rs  # 协作模式预设测试
```

## 依赖关系

- **上游依赖**：`codex-api`（ModelsClient、ReqwestTransport）、`codex-protocol`（ModelInfo、ModelPreset、ModelsResponse）、`codex-otel`（遥测认证模式）
- **内部依赖**：`config/`（Config）、`auth/`（AuthManager、CodexAuth）、`default_client`（构建 reqwest 客户端）
- **被依赖方**：`tasks/`（SessionTaskContext 提供 models_manager）、`codex/`（Session 初始化时创建 ModelsManager）、`state/`（SessionServices 持有 ModelsManager）

## 核心接口/API

| 接口/类型 | 说明 |
|---|---|
| `ModelsManager` | 模型管理器，提供 `refresh_models()`、`get_models()`、`resolve_model_info()` 等 |
| `ModelsCacheManager` | 磁盘缓存管理器，管理模型列表的 JSON 缓存文件（TTL 默认 5 分钟） |
| `with_config_overrides()` | 将用户配置覆盖（context_window、reasoning_summaries 等）应用到 ModelInfo |
| `model_info_from_slug()` | 为未知模型 slug 构建回退 ModelInfo |
| `client_version_to_whole()` | 将 Cargo 版本号转换为语义版本字符串 |
| `CollaborationModesConfig` | 协作模式配置（plan/execute/pair_programming 等模式的指令模板） |
| `BASE_INSTRUCTIONS` | 基础系统指令（来自 `prompt.md`） |
