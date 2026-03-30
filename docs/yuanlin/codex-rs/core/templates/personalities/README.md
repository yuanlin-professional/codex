# templates/personalities/ -- AI 人格风格模板

## 功能概述

`personalities/` 包含不同 AI 人格风格的提示词模板。人格模板定义了 AI 代理的沟通风格偏好，目前针对 gpt-5.2-codex 模型提供"友好型"（friendly）和"务实型"（pragmatic）两种风格。人格模板通过 `{{ personality }}` 占位符注入到模型指令模板中。

## 目录结构

```
personalities/
├── gpt-5.2-codex_friendly.md    # 友好型人格
└── gpt-5.2-codex_pragmatic.md   # 务实型人格
```

## 依赖关系

- **被引用方**：`src/models_manager/model_info.rs`（根据人格配置选择对应模板注入 `{{ personality }}` 占位符）

## 核心接口/API

| 文件 | 说明 |
|---|---|
| `gpt-5.2-codex_friendly.md` | 友好型人格："优化团队士气和支持性协作，与代码质量同等重要" |
| `gpt-5.2-codex_pragmatic.md` | 务实型人格："深度务实、高效的软件工程师" |
