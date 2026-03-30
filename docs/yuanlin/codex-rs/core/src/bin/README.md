# bin/ -- 二进制入口

## 功能概述

`bin/` 包含 Codex core crate 的可执行二进制入口文件。目前主要提供配置 JSON Schema 生成工具，用于导出 Codex 配置文件的 JSON Schema 以供编辑器验证和文档生成使用。

## 目录结构

```
bin/
└── config_schema.rs    # 配置 JSON Schema 生成可执行文件入口
```

## 依赖关系

- **上游依赖**：`schemars`（JSON Schema 生成）
- **内部依赖**：`config/`（配置类型定义）
- **被依赖方**：构建系统（Cargo）编译为独立可执行文件

## 核心接口/API

| 接口/类型 | 说明 |
|---|---|
| `config_schema` 二进制 | 输出 Codex 配置文件的 JSON Schema |
