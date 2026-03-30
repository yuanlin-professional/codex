# `escalation_policy.rs` — 提升策略 trait
定义 `EscalationPolicy` 异步 trait，其 `determine_action` 方法根据拦截的可执行文件路径、参数和工作目录返回 `EscalationDecision`（Run/Escalate/Deny）。
