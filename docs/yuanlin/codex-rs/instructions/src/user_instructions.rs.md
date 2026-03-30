# `user_instructions.rs` — 用户指令与技能指令

## 功能概述

定义 `UserInstructions`（来自 AGENTS.md 的用户指令）和 `SkillInstructions`（技能指令）的数据结构及其序列化为 ResponseItem 的转换逻辑。用户指令包装为 `# AGENTS.md instructions for {directory}` 格式，技能指令包装为 `<skill>` XML 标签格式。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `UserInstructions` | struct | 用户指令：directory + text |
| `SkillInstructions` | struct | 技能指令：name + path + contents |

## 依赖关系

- **被引用方**: core 提示构建、rollout 存储
