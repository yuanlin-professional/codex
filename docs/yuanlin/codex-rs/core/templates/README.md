# core/templates - 提示词模板

## 功能概述

此目录包含 Codex 核心引擎使用的各类提示词（prompt）模板。这些模板以 Markdown 和 XML 格式存储，定义了 AI 代理在不同模式和场景下的行为指令。

## 目录结构

| 子目录 | 说明 |
|--------|------|
| `agents/` | 多代理编排模板（orchestrator 指令） |
| `collab/` | 协作模式实验性提示词 |
| `collaboration_mode/` | 协作模式提示词（默认、执行、结对编程、计划） |
| `compact/` | 上下文压缩提示词（对话摘要） |
| `memories/` | 记忆系统提示词（整合、读取、分阶段输入） |
| `model_instructions/` | 特定模型的指令模板 |
| `personalities/` | AI 人格风格模板（友好型、务实型） |
| `review/` | 会话回顾模板（成功/中断退出） |
| `search_tool/` | 搜索工具描述模板 |
