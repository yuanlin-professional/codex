# src/status/

## 功能概述

`status/` 模块负责状态输出的格式化和显示适配。它将协议级别的快照数据转换为稳定的显示结构，用于 `/status` 命令输出和底栏/状态行辅助显示，同时将渲染关注点与传输层代码分离。

核心子模块 `rate_limits` 是状态行使用量限制项的主要集成点：它将原始窗口快照转换为本地时间标签，并将数据分类为可用、过时或缺失。

## 目录结构

```
status/
├── mod.rs          # 模块入口，重导出核心类型
├── account.rs      # 账户显示数据（ChatGPT/ApiKey）
├── card.rs         # 状态卡片渲染（/status 命令的完整输出）
├── format.rs       # 字段格式化工具（标签对齐、行宽计算、截断）
├── helpers.rs      # 辅助函数（目录显示、Token 压缩、模型显示）
├── rate_limits.rs  # 速率限制和额度显示格式化
├── tests.rs        # 单元测试
└── snapshots/      # 渲染快照测试
```

## 核心接口/API

| 类型/函数 | 说明 |
|-----------|------|
| `StatusAccountDisplay` | 账户显示枚举（ChatGpt{email, plan} / ApiKey） |
| `new_status_output_with_rate_limits()` | 生成带速率限制的完整状态输出 |
| `format_directory_display()` | 格式化目录路径显示 |
| `format_tokens_compact()` | 压缩格式化 Token 使用量 |
| `plan_type_display_name()` | 获取计划类型的显示名称 |
| `RateLimitSnapshotDisplay` | 速率限制快照显示数据 |
| `RateLimitWindowDisplay` | 速率限制窗口显示数据 |
| `rate_limit_snapshot_display_for_limit()` | 为特定限制生成显示数据 |
| `StatusRateLimitRow` | 速率限制行（标签 + 值） |
| `StatusRateLimitValue` | 速率限制值变体（Window/Credits/Stale/Missing） |
| `FieldFormatter` | 字段格式化器（标签宽度对齐） |
