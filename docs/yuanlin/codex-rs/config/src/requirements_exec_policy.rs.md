# `requirements_exec_policy.rs` — 需求执行策略规则解析

## 功能概述

将 `requirements.toml` 中的 `[rules]` 段落反序列化并转换为 `codex-execpolicy` crate 使用的内部 `Policy` 类型。支持前缀匹配规则（prefix rules），每条规则包含一个模式（pattern）和一个决策（decision）。模式由 token 序列组成，每个 token 可以是单一字符串或多选项（any_of）。需求中不允许 `allow` 决策，仅允许 `prompt` 和 `forbidden`，因为需求规则会与其他配置合并并取最严格结果。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `RequirementsExecPolicy` | 结构体 | 封装 `codex_execpolicy::Policy`，带有基于规则指纹的 `PartialEq` 实现 |
| `RequirementsExecPolicyToml` | 结构体 | TOML 反序列化的规则集合，包含 `prefix_rules` 列表 |
| `RequirementsExecPolicyPrefixRuleToml` | 结构体 | 单条前缀规则：pattern + decision + 可选 justification |
| `RequirementsExecPolicyPatternTokenToml` | 结构体 | 模式 token：`token`（单一匹配）或 `any_of`（多选匹配），二选一 |
| `RequirementsExecPolicyDecisionToml` | 枚举 | TOML 决策类型：Allow / Prompt / Forbidden |
| `RequirementsExecPolicyParseError` | 枚举 | 规则解析错误类型（空规则、空模式、无效 token、缺失决策、不允许的 Allow 等） |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `RequirementsExecPolicyToml::to_policy` | `(&self) -> Result<Policy, ParseError>` | 将 TOML 规则转换为 `codex_execpolicy::Policy` |
| `RequirementsExecPolicyToml::to_requirements_policy` | `(&self) -> Result<RequirementsExecPolicy, ParseError>` | 转换并包装为 `RequirementsExecPolicy` |
| `parse_pattern_token` | `(token, rule_index, token_index) -> Result<PatternToken, ParseError>` | 解析单个模式 token，验证非空和互斥约束 |

## 依赖关系

- **引用的 crate**: `codex_execpolicy`、`multimap`、`serde`、`thiserror`
- **被引用方**: `config_requirements.rs` 在需求归一化时调用 `to_requirements_policy`

## 实现备注

- 规则的第一个 token 作为 `MultiMap` 的键用于快速查找（按程序名索引），如果第一个 token 是 `any_of`，则为每个可选项分别插入规则。
- `RequirementsExecPolicy` 的 `PartialEq` 基于排序后的规则指纹字符串比较，而非结构比较。
- `Allow` 决策被明确禁止，并提供详细的错误消息解释原因（需求规则取最严格结果）。
