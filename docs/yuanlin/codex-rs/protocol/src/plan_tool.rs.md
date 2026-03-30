# `plan_tool.rs` — 计划工具参数类型

`StepStatus` 枚举（Pending / InProgress / Completed）、`PlanItemArg` 结构体（步骤描述 + 状态）和 `UpdatePlanArgs` 结构体（说明 + 计划步骤列表）定义了 TODO/checklist 工具的输入参数格式。用于 Agent 在计划模式下向 TUI 报告任务步骤及其进度。
