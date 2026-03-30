# core-skills

## 功能概述

`codex-core-skills` 是 Codex 项目中技能系统的核心运行时 crate。它负责技能的发现、加载、管理、注入和渲染,将 SKILL.md 文件系统转化为模型可以理解和调用的能力。

核心职责包括:

- **技能发现与加载**: 从多个根目录(项目、用户、系统、管理员)递归扫描 SKILL.md 文件,解析 YAML frontmatter 提取元数据。
- **技能管理**: 通过 `SkillsManager` 提供带缓存的技能加载,支持按 cwd 缓存和按配置缓存两种策略。
- **技能注入**: 将启用的技能作为上下文片段注入模型提示词。
- **技能渲染**: 将技能列表渲染为模型可读的格式。
- **配置规则**: 通过配置层栈解析技能的启用/禁用规则。
- **环境变量依赖**: 收集技能声明的环境变量依赖信息。
- **远程技能**: 支持远程技能加载。
- **系统技能**: 管理内嵌系统技能的安装和卸载。
- **隐式调用检测**: 通过脚本目录和文档路径索引检测隐式技能调用。

## 架构说明

1. **SkillsManager** (`manager.rs`) -- 技能管理器:
   - 维护两级缓存: `cache_by_cwd`(按工作目录)和 `cache_by_config`(按配置状态)。
   - `skills_for_config()`: 基于配置的技能加载(避免跨会话缓存泄漏)。
   - `skills_for_cwd()`: 基于工作目录的技能加载。
   - 在初始化时自动安装/卸载系统技能。
   - 支持产品级别的技能过滤(`restriction_product`)。

2. **Loader** (`loader.rs`) -- 技能加载器:
   - `skill_roots()`: 从配置层栈收集技能根目录(项目 `.codex/skills/`、用户 `~/.agents/skills/`、系统 `$CODEX_HOME/skills/.system/`、管理员 `/etc/codex/skills/`)。
   - `load_skills_from_roots()`: BFS 扫描根目录,解析每个 SKILL.md。
   - `parse_skill_file()`: 提取 YAML frontmatter(name, description),加载 `agents/openai.yaml` 元数据(interface, dependencies, policy)。
   - 最大扫描深度 6 层,每根最多 2000 个目录。

3. **Model** (`model.rs`) -- 技能数据模型:
   - `SkillMetadata`: 名称、描述、接口、依赖、策略、路径、作用域。
   - `SkillLoadOutcome`: 加载结果,包含技能列表、错误列表、禁用路径集、隐式调用索引。
   - `SkillPolicy`: 控制隐式调用和产品限制。
   - `SkillInterface`: UI 展示信息(显示名称、图标、品牌色、默认提示)。
   - `SkillDependencies`: MCP 工具依赖声明。

4. **其他模块**:
   - `injection.rs`: 将技能注入模型上下文。
   - `render.rs`: 渲染技能列表为模型指令。
   - `config_rules.rs`: 从配置层栈解析启用/禁用规则。
   - `system.rs`: 系统技能安装/卸载(委托给 `codex-skills` crate)。
   - `env_var_dependencies.rs`: 技能环境变量依赖收集。
   - `invocation_utils.rs`: 隐式调用索引构建与检测。
   - `mention_counts.rs`: 技能名称出现次数统计。
   - `remote.rs`: 远程技能支持。

## 目录结构

```
core-skills/
  Cargo.toml
  src/
    lib.rs                      # 模块声明与公开导出
    manager.rs                  # SkillsManager 技能管理器
    loader.rs                   # 技能发现与加载
    model.rs                    # 技能数据模型
    injection.rs                # 技能注入
    render.rs                   # 技能渲染
    config_rules.rs             # 配置规则解析
    system.rs                   # 系统技能安装/卸载
    env_var_dependencies.rs     # 环境变量依赖
    invocation_utils.rs         # 隐式调用检测
    mention_counts.rs           # 名称计数
    remote.rs                   # 远程技能
    *_tests.rs                  # 各模块单元测试
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `codex-config` | `ConfigLayerStack`, `SkillsConfig` 配置类型 |
| `codex-skills` | 系统技能安装(嵌入式资源) |
| `codex-instructions` | 技能指令类型 |
| `codex-protocol` | `SkillScope`, `Product` 等协议类型 |
| `codex-app-server-protocol` | `ConfigLayerSource` |
| `codex-analytics` / `codex-otel` | 遥测与分析 |
| `codex-login` | 认证 |
| `codex-utils-absolute-path` / `codex-utils-plugins` | 路径和插件工具 |
| `serde_yaml` | SKILL.md frontmatter 和 openai.yaml 解析 |
| `serde` / `serde_json` / `toml` | 配置解析 |
| `dirs` | 用户主目录 |
| `dunce` | 路径规范化 |
| `shlex` | Shell 命令解析 |
| `zip` | ZIP 文件处理 |
| `tokio` | 异步运行时 |
| `tracing` | 日志记录 |
| `anyhow` | 错误处理 |

## 核心接口/API

### SkillsManager

```rust
pub struct SkillsManager { /* codex_home, restriction_product, caches */ }

impl SkillsManager {
    pub fn new(codex_home: PathBuf, bundled_skills_enabled: bool) -> Self;
    pub fn skills_for_config(&self, input: &SkillsLoadInput) -> SkillLoadOutcome;
    pub async fn skills_for_cwd(&self, input: &SkillsLoadInput, force_reload: bool) -> SkillLoadOutcome;
    pub fn clear_cache(&self);
}

pub struct SkillsLoadInput {
    pub cwd: PathBuf,
    pub effective_skill_roots: Vec<PathBuf>,
    pub config_layer_stack: ConfigLayerStack,
    pub bundled_skills_enabled: bool,
}
```

### 技能数据模型

```rust
pub struct SkillMetadata {
    pub name: String,
    pub description: String,
    pub path_to_skills_md: PathBuf,
    pub scope: SkillScope,
    pub interface: Option<SkillInterface>,
    pub dependencies: Option<SkillDependencies>,
    pub policy: Option<SkillPolicy>,
}

pub struct SkillLoadOutcome {
    pub skills: Vec<SkillMetadata>,
    pub errors: Vec<SkillError>,
    pub disabled_paths: HashSet<PathBuf>,
}
```
