# `policy.rs` — 策略核心逻辑

## 功能概述

`Policy` 结构体是整个策略检查的核心，管理程序规范集合、禁止程序正则列表和禁止子串模式。`check` 方法按优先级依次检查禁止程序名、禁止参数子串，最后在匹配的程序规范中查找合适的规则。还提供了策略自检功能，用于验证策略文件中内嵌的正例和反例。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `Policy` | 结构体 | 策略容器，包含按程序名索引的规范 MultiMap、禁止程序正则列表和禁止子串正则 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `(programs, forbidden_regexes, forbidden_substrings) -> Result<Self>` | 构造策略实例，将禁止子串编译为正则 |
| `check` | `(&self, exec_call) -> Result<MatchedExec>` | 对命令执行完整的策略检查流程 |
| `check_each_good_list_individually` | `(&self) -> Vec<PositiveExampleFailedCheck>` | 验证策略中所有正例都能通过检查 |
| `check_each_bad_list_individually` | `(&self) -> Vec<NegativeExamplePassedCheck>` | 验证策略中所有反例都被拒绝 |

## 依赖关系

- **引用的 crate**: `multimap`、`regex_lite`
- **被引用方**: `execv_checker.rs`、`main.rs`、所有测试文件

## 实现备注

- 检查顺序：禁止程序正则 -> 禁止参数子串 -> 程序规范匹配。
- 当存在多个同名程序的规范时，依次尝试直到找到匹配项或返回最后一个错误。
