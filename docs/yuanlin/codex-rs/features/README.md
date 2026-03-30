# features

## 功能概述

`codex-features` 是 Codex 项目的集中式特性标志(Feature Flags)管理 crate。它定义了所有可切换的功能特性,管理其生命周期阶段,并提供从配置输入解析出有效特性集的逻辑。

核心职责包括:

- **特性注册表**: 以静态数组 `FEATURES` 定义所有特性标志,每个标志包含唯一 ID、配置键、生命周期阶段和默认启用状态。
- **生命周期管理**: 特性分为 `UnderDevelopment`(开发中)、`Experimental`(实验性)、`Stable`(稳定)、`Deprecated`(已弃用)、`Removed`(已移除) 五个阶段。
- **特性解析**: 从多个配置来源(base 配置、profile 配置、CLI 覆盖)合成最终的特性集。
- **遗留兼容**: 支持旧版特性键名的映射和弃用提示。
- **遥测集成**: 将非默认状态的特性变更报告给遥测系统。

## 架构说明

1. **Feature 枚举** -- 定义了约 50+ 个功能特性,涵盖:
   - Shell 工具: `ShellTool`, `UnifiedExec`, `ShellZshFork`, `ShellSnapshot`
   - 代码模式: `JsRepl`, `CodeMode`, `CodeModeOnly`
   - 沙箱: `UseLegacyLandlock`, `WindowsSandbox`
   - 搜索: `WebSearchRequest`, `WebSearchCached`
   - 协作: `Collab`, `MultiAgentV2`, `SpawnCsv`
   - 应用/插件: `Apps`, `ToolSearch`, `ToolSuggest`, `Plugins`
   - UI/UX: `Personality`, `FastMode`, `RealtimeConversation`
   - 以及许多其他实验性功能

2. **FeatureSpec** -- 静态注册表条目:
   ```rust
   pub struct FeatureSpec {
       pub id: Feature,
       pub key: &'static str,     // TOML 配置键
       pub stage: Stage,           // 生命周期阶段
       pub default_enabled: bool,  // 默认启用状态
   }
   ```

3. **Features** -- 有效特性集容器:
   - `with_defaults()`: 从注册表默认值初始化。
   - `apply_map()`: 应用 `[features]` TOML 表中的开关。
   - `from_sources()`: 从 base + profile + overrides 三层来源合成。
   - `normalize_dependencies()`: 处理特性间依赖(如 `SpawnCsv` 需要 `Collab`)。

4. **遗留特性系统** (`legacy.rs`):
   - 处理旧版配置键到新键的映射。
   - 记录遗留用法警告,帮助用户迁移到新的配置格式。

5. **实验性菜单**: `Stage::Experimental` 变体携带 `name`、`menu_description`、`announcement` 字段,支持 TUI 中的 `/experimental` 菜单渲染。

## 目录结构

```
features/
  Cargo.toml
  src/
    lib.rs       # Feature 枚举、FeatureSpec 注册表、Features 容器、解析逻辑
    legacy.rs    # 遗留特性键映射和向后兼容
    tests.rs     # 单元测试
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `codex-login` | `AuthManager`、`CodexAuth`,用于判断 Apps 功能是否可用 |
| `codex-otel` | `SessionTelemetry`,用于特性状态遥测 |
| `codex-protocol` | 事件类型(`Event`、`EventMsg`、`WarningEvent`) |
| `schemars` | JSON Schema 生成(配合 `FeaturesToml`) |
| `serde` | 序列化/反序列化 |
| `toml` | TOML 表类型 |
| `tracing` | 日志记录 |

## 核心接口/API

### Feature 枚举

```rust
pub enum Feature {
    GhostCommit, ShellTool, JsRepl, CodeMode, UnifiedExec,
    WebSearchRequest, Apps, Plugins, FastMode, /* ... 50+ variants ... */
}

impl Feature {
    pub fn key(self) -> &'static str;
    pub fn stage(self) -> Stage;
    pub fn default_enabled(self) -> bool;
}
```

### Features 容器

```rust
pub struct Features { /* BTreeSet<Feature> */ }

impl Features {
    pub fn with_defaults() -> Self;
    pub fn enabled(&self, f: Feature) -> bool;
    pub fn enable(&mut self, f: Feature) -> &mut Self;
    pub fn disable(&mut self, f: Feature) -> &mut Self;
    pub fn apply_map(&mut self, m: &BTreeMap<String, bool>);
    pub fn from_sources(base: FeatureConfigSource, profile: FeatureConfigSource, overrides: FeatureOverrides) -> Self;
    pub fn normalize_dependencies(&mut self);
    pub fn emit_metrics(&self, otel: &SessionTelemetry);
    pub async fn apps_enabled(&self, auth_manager: Option<&AuthManager>) -> bool;
}
```

### 配置集成

```rust
/// TOML [features] 表
pub struct FeaturesToml {
    pub entries: BTreeMap<String, bool>,
}

/// 根据键名查找特性
pub fn feature_for_key(key: &str) -> Option<Feature>;

/// 检查键名是否为已知特性
pub fn is_known_feature_key(key: &str) -> bool;
```
