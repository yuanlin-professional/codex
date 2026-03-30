# `account.rs` — 用户账户计划类型定义

## 功能概述

定义了 Codex 平台中用户账户的计划类型枚举 `PlanType`，涵盖 Free、Go、Plus、Pro、Team、Business、Enterprise、Edu 等各种订阅级别。提供了计划族分类辅助方法，用于判断某个计划是否属于"Team 类"或"Business 类"，以支持基于计划的权限与功能策略。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `PlanType` | 枚举 | 用户订阅计划类型，包含 Free / Go / Plus / Pro / Team / SelfServeBusinessUsageBased / Business / EnterpriseCbpUsageBased / Enterprise / Edu / Unknown 等变体 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `is_team_like` | `fn is_team_like(self) -> bool` | 判断计划是否属于 Team 或 SelfServeBusinessUsageBased |
| `is_business_like` | `fn is_business_like(self) -> bool` | 判断计划是否属于 Business 或 EnterpriseCbpUsageBased |

## 依赖关系

- **引用的 crate**: `schemars`, `serde`, `ts_rs`
- **被引用方**: `app-server-protocol` 中的 `Account` 类型等

## 实现备注

- 使用 `#[serde(other)]` 使得 `Unknown` 变体能兜底反序列化未知的计划字符串。
- 部分变体（如 `SelfServeBusinessUsageBased`）使用了自定义的序列化名称（snake_case 格式）以匹配 API wire format。
- 包含单元测试验证序列化/反序列化的正确性以及族分类方法的行为。
