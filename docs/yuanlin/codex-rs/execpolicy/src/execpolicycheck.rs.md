# `execpolicycheck.rs` — CLI 策略检查命令实现

## 功能概述

实现了 `codex-execpolicy check` 子命令的核心逻辑。`ExecPolicyCheckCommand` 使用 clap 定义命令行参数（规则文件路径、命令 token、是否美化输出、是否解析宿主可执行文件），`run` 方法加载策略文件、执行命令匹配并输出 JSON 结果。还提供了 `load_policies` 和 `format_matches_json` 辅助函数供其他模块复用。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ExecPolicyCheckCommand` | 结构体 | CLI 检查命令参数，包含 rules 路径列表、pretty 标志、resolve_host_executables 标志和 command token 列表 |
| `ExecPolicyCheckOutput` | 结构体 (内部) | JSON 输出格式，包含匹配规则列表和最终决策 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `run` | `(&self) -> Result<()>` | 执行策略检查并打印 JSON 结果 |
| `load_policies` | `(policy_paths) -> Result<Policy>` | 加载并解析多个策略文件，合并为单一 Policy |
| `format_matches_json` | `(matched_rules, pretty) -> Result<String>` | 将匹配结果格式化为 JSON 字符串 |

## 依赖关系

- **引用的 crate**: `clap`、`serde`、`serde_json`、`anyhow`
- **被引用方**: `main.rs`

## 实现备注

- 支持加载多个策略文件，通过 `PolicyParser` 依次解析后构建合并策略。
- 最终决策取所有匹配规则中最严格的决策。
