# plugin

## 功能概述

`codex-plugin` 是 Codex 项目的插件系统基础 crate。它定义了插件标识符、加载结果、能力摘要和遥测元数据等共享类型,为上层插件管理器(位于 `codex-core`)提供核心抽象。

核心职责包括:

- **插件标识符**: 定义 `PluginId` 类型,格式为 `<plugin_name>@<marketplace_name>`,支持解析和校验。
- **加载结果**: 定义 `PluginLoadOutcome<M>`,聚合所有已加载插件的技能根、MCP 服务器、App 连接器等信息。
- **能力摘要**: 定义 `PluginCapabilitySummary`,描述插件提供的能力(技能、MCP 服务器、App 连接器)。
- **遥测元数据**: 定义 `PluginTelemetryMetadata`,用于遥测上报。
- **插件命名空间**: 通过 `plugin_namespace_for_skill_path()` 从文件路径反查插件归属。

## 架构说明

1. **PluginId** (`plugin_id.rs`) -- 插件标识符:
   - 格式: `<plugin_name>@<marketplace_name>`
   - `parse()`: 从字符串解析,要求包含 `@` 分隔符。
   - `validate_plugin_segment()`: 校验名称段仅包含 ASCII 字母、数字、`_`、`-`。
   - `as_key()`: 重新序列化为字符串键。

2. **LoadedPlugin / PluginLoadOutcome** (`load_outcome.rs`) -- 加载结果:
   - `LoadedPlugin<M>`: 单个已加载插件,包含:
     - `config_name`: 配置中的名称
     - `manifest_name` / `manifest_description`: 清单文件中的名称和描述
     - `root`: 插件根目录(绝对路径)
     - `enabled`: 是否启用
     - `skill_roots` / `disabled_skill_paths`: 技能目录和禁用路径
     - `mcp_servers`: MCP 服务器定义(泛型 `M`)
     - `apps`: App 连接器 ID 列表
     - `error`: 加载错误信息
   - `PluginLoadOutcome<M>`: 聚合所有插件的加载结果:
     - `effective_skill_roots()`: 收集所有活跃插件的技能根目录。
     - `effective_mcp_servers()`: 合并所有活跃插件的 MCP 服务器。
     - `effective_apps()`: 收集所有活跃插件的 App 连接器(去重)。
     - `capability_summaries()`: 返回各插件的能力摘要。
   - `EffectiveSkillRoots` trait: 使调用者无需知道 MCP 泛型参数即可获取技能根。
   - `prompt_safe_plugin_description()`: 标准化描述文本(合并空白,截断至 1024 字符)。

3. **PluginCapabilitySummary** (`lib.rs`) -- 能力摘要:
   - `config_name` / `display_name` / `description`
   - `has_skills`: 是否有技能
   - `mcp_server_names`: MCP 服务器名称列表
   - `app_connector_ids`: App 连接器 ID 列表

4. **PluginTelemetryMetadata** (`lib.rs`) -- 遥测元数据,包含 `PluginId` 和可选的 `PluginCapabilitySummary`。

5. **插件命名空间** (来自 `codex-utils-plugins`,在此重新导出):
   - `plugin_namespace_for_skill_path()`: 通过向上遍历祖先目录查找 `.codex-plugin/plugin.json` 清单文件,返回插件名称。
   - `PLUGIN_MANIFEST_PATH`: 清单文件相对路径 `.codex-plugin/plugin.json`。
   - `mention_syntax()`: 生成插件提及语法。

## 目录结构

```
plugin/
  Cargo.toml
  src/
    lib.rs               # 模块声明,PluginCapabilitySummary, PluginTelemetryMetadata
    plugin_id.rs         # PluginId 解析与校验
    load_outcome.rs      # LoadedPlugin, PluginLoadOutcome, EffectiveSkillRoots
    plugin_namespace.rs  # 插件命名空间解析(实际实现在 codex-utils-plugins)
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `codex-utils-absolute-path` | `AbsolutePathBuf` 类型 |
| `codex-utils-plugins` | `plugin_namespace_for_skill_path()`, `PLUGIN_MANIFEST_PATH`, `mention_syntax()` |
| `thiserror` | 错误类型派生 |

## 核心接口/API

### 插件标识符

```rust
pub struct PluginId {
    pub plugin_name: String,
    pub marketplace_name: String,
}

impl PluginId {
    pub fn new(plugin_name: String, marketplace_name: String) -> Result<Self, PluginIdError>;
    pub fn parse(plugin_key: &str) -> Result<Self, PluginIdError>;
    pub fn as_key(&self) -> String;  // "name@marketplace"
}

/// 校验名称段(仅 ASCII 字母、数字、_、-)
pub fn validate_plugin_segment(segment: &str, kind: &str) -> Result<(), String>;
```

### 加载结果

```rust
pub struct LoadedPlugin<M> {
    pub config_name: String,
    pub root: AbsolutePathBuf,
    pub enabled: bool,
    pub skill_roots: Vec<PathBuf>,
    pub mcp_servers: HashMap<String, M>,
    pub apps: Vec<AppConnectorId>,
    pub error: Option<String>,
}

pub struct PluginLoadOutcome<M> { /* plugins, capability_summaries */ }

impl<M: Clone> PluginLoadOutcome<M> {
    pub fn from_plugins(plugins: Vec<LoadedPlugin<M>>) -> Self;
    pub fn effective_skill_roots(&self) -> Vec<PathBuf>;
    pub fn effective_mcp_servers(&self) -> HashMap<String, M>;
    pub fn effective_apps(&self) -> Vec<AppConnectorId>;
    pub fn capability_summaries(&self) -> &[PluginCapabilitySummary];
}

pub trait EffectiveSkillRoots {
    fn effective_skill_roots(&self) -> Vec<PathBuf>;
}
```

### 能力摘要

```rust
pub struct PluginCapabilitySummary {
    pub config_name: String,
    pub display_name: String,
    pub description: Option<String>,
    pub has_skills: bool,
    pub mcp_server_names: Vec<String>,
    pub app_connector_ids: Vec<AppConnectorId>,
}
```
