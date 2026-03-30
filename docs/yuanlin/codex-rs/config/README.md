# config

## 功能概述

`codex-config` 是 Codex 项目的配置管理核心 crate。它负责从多个配置来源(系统级、用户级、项目级、MDM、云端需求等)加载 TOML 配置,按优先级合并,并提供约束校验和诊断能力。

核心职责包括:

- **多层配置加载**: 支持系统(system)、用户(user)、项目(project)、MDM、会话标志(session flags)等多种配置层。
- **优先级合并**: 按优先级从低到高合并 TOML 值,高优先级覆盖低优先级。
- **云端需求约束**: 加载和应用来自云端的配置需求(如安全策略、网络约束等)。
- **配置诊断**: 提供详细的配置错误报告,包含文本位置信息。
- **配置指纹**: 为 TOML 配置生成版本指纹,用于变更检测。
- **Skills 配置**: 支持技能系统的配置项(捆绑技能开关等)。
- **项目根标记**: 通过可配置的文件标记(如 `.git`、`package.json`)确定项目根目录。

## 架构说明

该 crate 采用分层架构设计:

1. **ConfigLayerStack** (`state.rs`) -- 配置层栈,核心数据结构:
   - 维护一个按优先级排序的 `ConfigLayerEntry` 列表(从低到高)。
   - 每个层包含名称(`ConfigLayerSource`)、TOML 值、原始文本和版本指纹。
   - 提供 `effective_config()` 合并视图和 `origins()` 字段溯源。
   - 支持动态插入/替换用户配置层。

2. **配置需求系统**:
   - `config_requirements.rs` -- 定义 `ConfigRequirementsToml`,包含 feature 需求、应用需求、网络约束、沙箱模式需求等。
   - `cloud_requirements.rs` -- 从云端加载配置需求(`CloudRequirementsLoader`)。
   - `requirements_exec_policy.rs` -- 执行策略需求,定义命令前缀规则。
   - `constraint.rs` -- 通用约束结果类型 `Constrained<T>`。

3. **合并逻辑** (`merge.rs`) -- 递归合并两个 TOML 值,表(table)级别递归合并,标量级别覆盖。

4. **诊断系统** (`diagnostics.rs`) -- 配置错误报告,支持文本范围定位(`TextPosition`、`TextRange`),提供格式化的错误消息。

5. **指纹系统** (`fingerprint.rs`) -- 为 TOML 配置计算 SHA-256 指纹,用于检测配置变更。

6. **辅助模块**:
   - `overrides.rs` -- 从 CLI 参数构建配置覆盖层。
   - `project_root_markers.rs` -- 项目根目录标记文件配置。
   - `skills_config.rs` -- 技能系统配置(捆绑技能开关等)。

## 目录结构

```
config/
  Cargo.toml
  src/
    lib.rs                      # 模块声明与公开导出
    state.rs                    # ConfigLayerStack, ConfigLayerEntry 核心状态
    merge.rs                    # TOML 值递归合并
    config_requirements.rs      # 配置需求定义(feature/network/sandbox/MCP等)
    cloud_requirements.rs       # 云端需求加载器
    requirements_exec_policy.rs # 执行策略需求
    constraint.rs               # 约束结果类型
    diagnostics.rs              # 配置错误诊断与格式化
    fingerprint.rs              # TOML 配置指纹(SHA-256)
    overrides.rs                # CLI 覆盖层构建
    project_root_markers.rs     # 项目根标记文件
    skills_config.rs            # 技能系统配置
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `codex-app-server-protocol` | `ConfigLayer`, `ConfigLayerSource`, `ConfigLayerMetadata` 类型 |
| `codex-execpolicy` | 执行策略相关类型 |
| `codex-protocol` | 协议级类型定义 |
| `codex-utils-absolute-path` | 绝对路径工具 |
| `toml` / `toml_edit` | TOML 解析与编辑 |
| `serde` / `serde_json` | 序列化/反序列化 |
| `serde_path_to_error` | 带路径的反序列化错误 |
| `schemars` | JSON Schema 生成 |
| `sha2` | SHA-256 配置指纹 |
| `thiserror` | 错误类型派生 |
| `tokio` | 异步文件 I/O |
| `futures` | 异步工具 |
| `multimap` | 多值映射 |
| `tracing` | 日志记录 |

## 核心接口/API

### ConfigLayerStack

```rust
pub struct ConfigLayerStack { /* layers, user_layer_index, requirements */ }

impl ConfigLayerStack {
    /// 创建新的配置层栈(验证优先级顺序)
    pub fn new(layers: Vec<ConfigLayerEntry>, requirements: ConfigRequirements, ...) -> io::Result<Self>;

    /// 获取合并后的有效配置
    pub fn effective_config(&self) -> TomlValue;

    /// 获取字段来源映射
    pub fn origins(&self) -> HashMap<String, ConfigLayerMetadata>;

    /// 从高到低返回配置层
    pub fn layers_high_to_low(&self) -> Vec<&ConfigLayerEntry>;

    /// 替换或插入用户配置层
    pub fn with_user_config(&self, config_toml: &AbsolutePathBuf, user_config: TomlValue) -> Self;
}
```

### 合并

```rust
/// 递归合并 TOML 值,overlay 覆盖 base
pub fn merge_toml_values(base: &mut TomlValue, overlay: &TomlValue);
```

### 配置需求

```rust
pub struct ConfigRequirementsToml {
    // features, apps, network, sandbox_mode, mcp_servers 等需求
}

pub struct ConfigRequirements { /* 解析后的需求约束 */ }
```
