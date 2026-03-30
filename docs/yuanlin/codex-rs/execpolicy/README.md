# execpolicy

## 功能概述

`codex-execpolicy` 是 Codex 的命令执行策略引擎，基于前缀匹配的 Starlark 规则来决定命令是否可以执行。它为 Codex 提供了一套灵活的策略评估系统，能够对命令进行 **Allow（允许）**、**Prompt（提示用户确认）** 或 **Forbidden（禁止）** 三种决策。

该 crate 同时提供库（`codex_execpolicy`）和二进制可执行文件（`codex-execpolicy`），二进制文件可作为独立的策略检查工具使用。

## 架构说明

```
                    +-----------------+
                    |   PolicyParser  |  (Starlark 规则解析)
                    +--------+--------+
                             |
                             v
                    +-----------------+
                    |     Policy      |  (策略容器，持有规则集合)
                    +--------+--------+
                             |
               +-------------+-------------+
               |                           |
               v                           v
      +----------------+         +------------------+
      |  PrefixRule    |         |  NetworkRule      |
      |  (命令前缀规则) |         |  (网络访问规则)    |
      +-------+--------+         +--------+---------+
              |                            |
              v                            v
      +----------------+         +------------------+
      |   RuleMatch    |         |    Decision       |
      | (匹配结果)      |         | Allow/Prompt/     |
      +----------------+         | Forbidden         |
                                 +------------------+
```

## 目录结构

```
execpolicy/
  src/
    lib.rs              # 模块声明与公共 API re-export
    main.rs             # CLI 二进制入口
    amend.rs            # 策略追加功能（追加 allow 前缀规则、网络规则）
    decision.rs         # Decision 枚举定义 (Allow / Prompt / Forbidden)
    error.rs            # 错误类型定义
    execpolicycheck.rs  # ExecPolicyCheckCommand 命令行检查接口
    executable_name.rs  # 可执行文件名解析工具
    parser.rs           # PolicyParser - Starlark 规则文件解析器
    policy.rs           # Policy 核心结构与命令检查逻辑
    rule.rs             # Rule trait、PrefixRule、NetworkRule、PatternToken 等
  Cargo.toml
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `starlark` | 使用 Starlark 语言解析和评估策略规则文件 |
| `codex-utils-absolute-path` | 绝对路径处理工具 |
| `multimap` | 多值映射，按程序名索引多条规则 |
| `shlex` | Shell 词法分析，用于命令拆分 |
| `serde` / `serde_json` | 序列化/反序列化策略和评估结果 |
| `clap` | CLI 参数解析 |
| `anyhow` / `thiserror` | 错误处理 |

## 核心接口/API

### `Decision` 枚举

```rust
pub enum Decision {
    Allow,     // 命令可以直接运行
    Prompt,    // 需要用户确认
    Forbidden, // 命令被禁止
}
```

### `Policy` 结构体

策略的核心容器，持有命令前缀规则和网络规则：

```rust
impl Policy {
    // 创建空策略
    pub fn empty() -> Self;

    // 检查单个命令，返回评估结果
    pub fn check<F>(&self, cmd: &[String], heuristics_fallback: &F) -> Evaluation;

    // 带选项检查
    pub fn check_with_options<F>(&self, cmd: &[String], heuristics_fallback: &F, options: &MatchOptions) -> Evaluation;

    // 批量检查多个命令
    pub fn check_multiple<Commands, F>(&self, commands: Commands, heuristics_fallback: &F) -> Evaluation;

    // 添加前缀规则
    pub fn add_prefix_rule(&mut self, prefix: &[String], decision: Decision) -> Result<()>;

    // 添加网络规则
    pub fn add_network_rule(&mut self, host: &str, protocol: NetworkRuleProtocol, decision: Decision, justification: Option<String>) -> Result<()>;

    // 合并叠加策略
    pub fn merge_overlay(&self, overlay: &Policy) -> Policy;

    // 获取编译后的网络域名白名单/黑名单
    pub fn compiled_network_domains(&self) -> (Vec<String>, Vec<String>);
}
```

### `PolicyParser`

从 Starlark 格式文件解析生成 `Policy` 对象。

### `Rule` trait

```rust
pub trait Rule: Any + Debug + Send + Sync {
    fn program(&self) -> &str;
    fn matches(&self, cmd: &[String]) -> Option<RuleMatch>;
    fn as_any(&self) -> &dyn Any;
}
```

### `Evaluation` 结构体

```rust
pub struct Evaluation {
    pub decision: Decision,           // 最终决策
    pub matched_rules: Vec<RuleMatch>, // 匹配到的规则列表
}
```

### 策略追加函数

```rust
// 追加 allow 前缀规则到策略文件
pub fn blocking_append_allow_prefix_rule(...) -> Result<(), AmendError>;

// 追加网络规则到策略文件
pub fn blocking_append_network_rule(...) -> Result<(), AmendError>;
```
