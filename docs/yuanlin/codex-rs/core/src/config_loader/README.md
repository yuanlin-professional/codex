# config_loader/ -- 配置加载器

## 功能概述

`config_loader/` 负责 Codex 配置文件的多层加载基础设施。它处理从系统级（`/etc/codex/config.toml`）、用户级（`~/.codex/config.toml`）、项目级（`.codex/config.toml`）以及 macOS 特定路径加载配置层，构建 `ConfigLayerStack`，并重导出 `codex-config` crate 中的核心配置类型（层栈、需求约束、错误类型等）。

## 目录结构

```
config_loader/
├── mod.rs        # 模块入口，重导出 codex-config 类型，定义加载和验证函数
├── layer_io.rs   # LoadedConfigLayers：从磁盘加载配置层的 I/O 逻辑
├── macos.rs      # macOS 特定的配置路径解析
├── README.md     # 原始文档
└── tests.rs      # 配置加载测试
```

## 依赖关系

- **上游依赖**：`codex-config`（核心配置层栈类型 `ConfigLayerStack`、`ConfigLayerEntry`、`ConfigRequirements` 等）、`codex-git-utils`（git 项目根解析）、`codex-protocol`（信任级别、沙箱模式）
- **内部依赖**：`config/`（ConfigToml）
- **被依赖方**：`config/`（Config 构建时使用 ConfigLayerStack 和各种加载器类型）

## 核心接口/API

| 接口/类型 | 说明 |
|---|---|
| `ConfigLayerStack` | 配置层栈，按优先级排列的配置层集合 |
| `ConfigLayerEntry` | 单个配置层条目（来源、内容、优先级） |
| `ConfigRequirements` | 配置需求约束（沙箱模式、网络、MCP 服务器等） |
| `LoaderOverrides` | 加载器覆盖参数 |
| `Sourced<T>` / `ConstrainedWithSource<T>` | 带来源标注的配置值 |
| `load_config_layers_state()` | 加载所有配置层状态 |
| `first_layer_config_error()` | 检查配置层中的首个错误 |
| `SYSTEM_CONFIG_TOML_FILE_UNIX` | Unix 系统级配置文件路径（`/etc/codex/config.toml`） |
