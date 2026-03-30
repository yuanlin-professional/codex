# agent/builtins/ -- 内置代理角色

## 功能概述

`builtins/` 存放 Codex 内置的代理角色定义文件。这些 TOML 配置文件定义了预置的子代理角色类型，在多代理场景中可直接使用，无需用户自定义角色配置。

## 目录结构

```
builtins/
├── awaiter.toml    # "awaiter" 角色：等待型代理配置
└── explorer.toml   # "explorer" 角色：探索型代理配置
```

## 依赖关系

- **被引用方**：`agent/role.rs`（角色解析时查找内置角色定义）

## 核心接口/API

| 文件 | 说明 |
|---|---|
| `awaiter.toml` | 等待型代理角色配置 |
| `explorer.toml` | 探索型代理角色配置 |
