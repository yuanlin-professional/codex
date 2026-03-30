# cli

## 功能概述

`codex-utils-cli` 提供了 Codex CLI 工具链中共享的命令行参数解析辅助类型，基于 `clap` 框架实现。包括审批模式（`--approval-mode`）、沙箱模式（`--sandbox`）的枚举映射，配置覆盖（`-c key=value`）的解析与应用，以及环境变量显示的格式化工具。

## 目录结构

```
cli/
├── Cargo.toml
└── src/
    ├── lib.rs                    # 模块导出
    ├── approval_mode_cli_arg.rs  # --approval-mode 参数枚举
    ├── sandbox_mode_cli_arg.rs   # --sandbox 参数枚举
    ├── config_override.rs        # -c key=value 配置覆盖
    └── format_env_display.rs     # 环境变量格式化显示
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `clap` | 命令行参数解析框架 |
| `codex-protocol` | 引用 `AskForApproval`、`SandboxPolicy` 等协议类型 |
| `serde` | 序列化支持 |
| `toml` | TOML 值解析（用于 -c 覆盖） |

## 核心接口/API

### `ApprovalModeCliArg`

clap `ValueEnum` 枚举，映射 CLI 参数到协议的 `AskForApproval`：

| CLI 值 | 协议值 | 说明 |
|--------|--------|------|
| `untrusted` | `UnlessTrusted` | 仅运行受信任命令 |
| `on-failure` | `OnFailure` | (已废弃) 失败时请求审批 |
| `on-request` | `OnRequest` | 模型决定何时请求审批 |
| `never` | `Never` | 从不请求审批 |

### `SandboxModeCliArg`

clap `ValueEnum` 枚举，映射到 `SandboxMode`：
- `read-only` / `workspace-write` / `danger-full-access`

### `CliConfigOverrides`

支持通过 `#[clap(flatten)]` 嵌入 CLI 结构体，收集 `-c key=value` 参数。

```rust
let overrides = CliConfigOverrides { raw_overrides: vec!["model=\"o3\"".into()] };
let parsed = overrides.parse_overrides()?;  // Vec<(String, toml::Value)>
overrides.apply_on_value(&mut config_value)?;  // 应用到 TOML 配置树
```

### `format_env_display(env, env_vars) -> String`

将环境变量映射格式化为 `KEY=*****` 形式的显示字符串（隐藏值），用于日志输出。
