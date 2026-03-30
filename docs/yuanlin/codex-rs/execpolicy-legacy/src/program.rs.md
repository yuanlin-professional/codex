# `program.rs` — 程序规范定义与命令检查

## 功能概述

定义了 `ProgramSpec`（程序规范）和 `MatchedExec`（匹配结果）等核心类型。`ProgramSpec` 描述了一个程序的完整策略规范，包括允许的选项、参数模式、系统路径、选项捆绑等配置。其 `check` 方法实现了完整的命令行参数解析流程：识别标志、解析带值选项、收集位置参数，然后验证必需选项并返回匹配结果。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `ProgramSpec` | 结构体 | 程序策略规范，包含程序名、系统路径、允许的选项/参数模式、禁止原因、正反例列表等 |
| `MatchedExec` | 枚举 | 匹配结果：`Match`（匹配成功，包含 ValidExec）或 `Forbidden`（被禁止，包含原因和触发源） |
| `Forbidden` | 枚举 | 禁止原因的触发源：`Program`（程序名被禁止）、`Arg`（参数包含禁止子串）、`Exec`（匹配但规范标记为禁止） |
| `PositiveExampleFailedCheck` | 结构体 | 正例验证失败的记录 |
| `NegativeExamplePassedCheck` | 结构体 | 反例验证意外通过的记录 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ProgramSpec::new` | `(program, system_path, ...) -> Self` | 构造程序规范 |
| `ProgramSpec::check` | `(&self, exec_call) -> Result<MatchedExec>` | 对命令进行完整的解析和策略匹配 |
| `verify_should_match_list` | `(&self) -> Vec<PositiveExampleFailedCheck>` | 验证策略中定义的正例 |
| `verify_should_not_match_list` | `(&self) -> Vec<NegativeExamplePassedCheck>` | 验证策略中定义的反例 |

## 依赖关系

- **引用的 crate**: `serde`
- **被引用方**: `policy.rs`、`policy_parser.rs`

## 实现备注

- 命令行解析逻辑：遍历参数列表，以 `-` 开头的识别为选项/标志，其余为位置参数。
- 不支持 `--` 分隔符（返回 DoubleDashNotSupportedYet 错误）。
- 选项后跟 `-` 开头的值被视为错误（OptionFollowedByOptionInsteadOfValue）。
