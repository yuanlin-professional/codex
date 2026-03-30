# `parser.rs` — Starlark 策略文件解析器（新版）

## 功能概述

将使用 Starlark DSL 编写的策略文件解析为 `Policy` 对象。与 legacy 版本不同，新版解析器支持前缀规则中的替代匹配（alternatives）、网络规则、宿主可执行文件声明以及正例/反例验证。解析器注册了 `prefix_rule`、`network_rule`、`host_executable` 三种内建函数，支持模式中的列表替代语法和多种示例格式（字符串或列表）。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `PolicyParser` | 结构体 | 策略解析器，封装 `PolicyBuilder`，支持增量解析多个策略文件 |
| `PolicyBuilder` | 结构体 (内部) | 在评估期间收集规则、网络规则、宿主可执行文件和待验证示例 |
| `PendingExampleValidation` | 结构体 (内部) | 待验证的正例/反例集合及其关联规则和位置信息 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `PolicyParser::new` | `() -> Self` | 创建空的解析器实例 |
| `PolicyParser::parse` | `(&mut self, identifier, contents) -> Result<()>` | 解析一个策略文件，累积规则到内部构建器 |
| `PolicyParser::build` | `(self) -> Policy` | 消耗解析器并构建最终的 Policy 对象 |
| `parse_pattern` | (内部) | 将 Starlark 值列表解析为 PatternToken 序列 |
| `parse_examples` | (内部) | 将 Starlark 值列表解析为示例命令列表 |
| `parse_network_rule_decision` | (内部) | 解析网络规则决策，支持 `deny` 作为 `forbidden` 的别名 |

## 依赖关系

- **引用的 crate**: `starlark`、`multimap`、`shlex`、`codex_utils_absolute_path`
- **被引用方**: `lib.rs`、`execpolicycheck.rs`、测试文件

## 实现备注

- 支持增量解析：同一个 `PolicyParser` 可以依次 `parse` 多个文件，规则会累积。
- 第一个模式 token 中的替代列表会展开为多条独立规则（按程序名索引），后续 token 的替代列表保持为 `PatternToken::Alts`。
- 示例验证在每次 `parse` 调用结束时执行，确保新增规则的正例/反例在解析时就被检查。
- 使用 `shlex::split` 解析字符串格式的示例，支持 shell 风格的引号和转义。
