# execpolicy-legacy

## 功能概述

`codex-execpolicy-legacy` 是 Codex 的遗留版本命令执行策略引擎，用于验证提议的 exec 调用是否合法。与新版 `execpolicy` 使用前缀匹配不同，遗留版本采用更细粒度的参数匹配方式，支持对命令的每个参数进行类型化验证。

该 crate 包含一份内嵌的默认策略文件（`default.policy`），使用 Starlark 语言编写，定义了常见命令的安全规则。它同时提供库和 CLI 二进制工具。

## 架构说明

```
               +-------------------+
               |   PolicyParser    | (Starlark 策略解析器)
               +---------+---------+
                         |
                         v
               +-------------------+
               |      Policy       | (策略容器)
               +---------+---------+
                         |
                         v
               +-------------------+
               |   ExecvChecker    | (exec 调用检查器)
               +---------+---------+
                         |
          +--------------+--------------+
          |              |              |
          v              v              v
   +-----------+  +-----------+  +-----------+
   |ProgramSpec|  | ExecCall  |  |ArgMatcher |
   |(程序规格)  |  |(exec 调用) |  |(参数匹配)  |
   +-----------+  +-----------+  +-----------+
```

## 目录结构

```
execpolicy-legacy/
  src/
    lib.rs           # 模块声明与公共 API re-export
    main.rs          # CLI 二进制入口
    arg_matcher.rs   # ArgMatcher - 参数匹配器
    arg_resolver.rs  # PositionalArg - 位置参数解析
    arg_type.rs      # ArgType - 参数类型定义
    error.rs         # 错误类型
    exec_call.rs     # ExecCall - exec 调用表示
    execv_checker.rs # ExecvChecker - exec 验证核心逻辑
    opt.rs           # Opt - 命令选项定义
    policy.rs        # Policy - 策略容器
    policy_parser.rs # PolicyParser - Starlark 策略解析
    program.rs       # ProgramSpec / MatchedExec / Forbidden 等
    sed_command.rs   # sed 命令特殊解析
    valid_exec.rs    # ValidExec / MatchedArg / MatchedFlag / MatchedOpt
    default.policy   # 内嵌默认 Starlark 策略文件
  Cargo.toml
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `starlark` | Starlark 语言运行时，用于解析策略文件 |
| `allocative` | 内存分配追踪（Starlark 要求） |
| `multimap` | 多值映射，按程序名索引规则 |
| `regex-lite` | 轻量正则表达式，用于参数模式匹配 |
| `path-absolutize` | 路径规范化 |
| `serde` / `serde_json` / `serde_with` | 序列化支持 |
| `clap` | CLI 参数解析 |
| `derive_more` | 派生宏扩展 |
| `log` / `env_logger` | 日志记录 |

## 核心接口/API

### `get_default_policy()`

```rust
pub fn get_default_policy() -> starlark::Result<Policy>;
```

加载内嵌的默认策略文件，返回解析后的 `Policy` 对象。

### `PolicyParser`

```rust
pub struct PolicyParser { ... }
impl PolicyParser {
    pub fn new(name: &str, content: &str) -> Self;
    pub fn parse(self) -> starlark::Result<Policy>;
}
```

### `ExecvChecker`

核心的 exec 调用验证器，根据策略判断命令是否合法。

### `ExecCall`

表示一个 exec 系统调用的参数，包含程序路径和参数列表。

### `ProgramSpec`

程序规格定义，描述一个程序允许的参数模式：

```rust
pub struct ProgramSpec { ... }
pub struct MatchedExec { ... }    // 匹配成功的 exec
pub struct Forbidden { ... }       // 被禁止的 exec
```

### `ArgMatcher`

参数匹配器，支持对单个参数进行类型化验证。

### `ValidExec`

```rust
pub struct ValidExec { ... }
pub struct MatchedArg { ... }
pub struct MatchedFlag { ... }
pub struct MatchedOpt { ... }
```

验证通过的 exec 调用及其匹配的参数、标志和选项。
