# templates/search_tool/ -- 搜索工具描述模板

## 功能概述

`search_tool/` 包含工具搜索（tool_search）和工具建议（tool_suggest）功能的描述模板。当启用了应用连接器时，这些模板用于生成工具搜索工具的描述，告知模型可以通过搜索发现更多工具。模板支持 `{{app_descriptions}}` 占位符，在运行时替换为实际的应用描述。

## 目录结构

```
search_tool/
├── tool_description.md          # tool_search 工具描述模板
└── tool_suggest_description.md  # tool_suggest 工具描述模板
```

## 依赖关系

- **被引用方**：`src/tools/spec.rs`（构建工具规格时加载描述模板）、`src/tools/handlers/tool_search.rs`

## 核心接口/API

| 文件 | 说明 |
|---|---|
| `tool_description.md` | 工具搜索描述：使用 BM25 搜索应用/连接器工具元数据，包含 `{{app_descriptions}}` 占位符 |
| `tool_suggest_description.md` | 工具建议描述模板 |
