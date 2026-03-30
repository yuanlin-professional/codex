# templates/model_instructions/ -- 模型专用指令模板

## 功能概述

`model_instructions/` 包含特定模型的指令模板。这些模板用于在模型的 `model_messages` 配置中提供定制化的系统指令，支持 `{{ personality }}` 占位符，在运行时根据用户选择的人格风格进行替换。

## 目录结构

```
model_instructions/
└── gpt-5.2-codex_instructions_template.md    # GPT-5.2-codex 模型指令模板
```

## 依赖关系

- **被引用方**：`src/models_manager/model_info.rs`（构建 ModelInfo 时使用模型指令模板）

## 核心接口/API

| 文件 | 说明 |
|---|---|
| `gpt-5.2-codex_instructions_template.md` | GPT-5.2-codex 模型系统指令模板，包含 `{{ personality }}` 占位符，运行时替换为选定的人格风格描述 |
