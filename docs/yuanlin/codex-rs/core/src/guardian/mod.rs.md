# `mod.rs` — Guardian 模块入口与核心配置

## 功能概述

Guardian 审查模块负责判断 `on-request` 级别的审批请求是否应自动批准而非展示给用户。整体方法是：(1) 重建紧凑的对话记录以保留用户意图和近期上下文；(2) 请求专用的 Guardian 审查 session 对计划操作进行风险评估并返回严格 JSON；(3) 超时、执行失败或格式错误时采用"关闭失败"策略（即默认拒绝）；(4) 仅批准低风险和中风险操作（`risk_score < 80`）。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `GuardianEvidence` | struct | Guardian 审查返回的证据项，含 `message` 和 `why` |
| `GuardianAssessment` | struct | Guardian 的结构化输出，含 `risk_level`、`risk_score`、`rationale`、`evidence` |

## 关键常量

| 名称 | 说明 |
|------|------|
| `GUARDIAN_PREFERRED_MODEL` | 首选模型 `"gpt-5.4"` |
| `GUARDIAN_REVIEW_TIMEOUT` | 审查超时 90 秒 |
| `GUARDIAN_REVIEWER_NAME` | 审查者名称 `"guardian"` |
| `GUARDIAN_APPROVAL_RISK_THRESHOLD` | 风险分数阈值 80，低于此值自动批准 |
| `GUARDIAN_MAX_*_TOKENS` | 各种 token 预算限制（消息/工具记录、条目、action 字符串） |
| `GUARDIAN_RECENT_ENTRY_LIMIT` | 最近条目限制 40 条 |

## 依赖关系

- **子模块**: `approval_request`、`prompt`、`review`、`review_session`
- **被引用方**: 审批处理流程（`on-request` 模式下的自动审查）

## 实现备注

- 模块采用"关闭失败"（fail-closed）策略：任何非正常完成路径都视为高风险拒绝。
- Guardian 审查 session 克隆父配置，因此继承父 turn 已有的网络代理/白名单设置。
- 测试中通过 `#[cfg(test)]` 条件编译额外导出内部函数以供集成测试使用。
