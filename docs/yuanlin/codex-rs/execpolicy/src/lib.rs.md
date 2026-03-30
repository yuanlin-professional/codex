# `lib.rs` — execpolicy 库入口与公开 API

## 功能概述

作为 `codex_execpolicy` crate 的入口文件，声明所有公开模块（amend、decision、error、execpolicycheck、parser、policy、rule）和一个内部模块（executable_name），并统一导出核心类型。提供了策略解析、命令匹配、决策评估以及策略文件动态修改等完整功能的公开 API。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| (公开导出) | 各种类型 | 导出了 Decision、Policy、PolicyParser、Rule、RuleRef、RuleMatch、Evaluation、MatchOptions、Error、ErrorLocation、TextPosition、TextRange、AmendError、ExecPolicyCheckCommand、NetworkRuleProtocol 等 |

## 依赖关系

- **被引用方**: `main.rs`、测试文件、上层 Codex runtime

## 实现备注

- `executable_name` 模块为内部模块（非 pub），仅在 crate 内部使用。
