# `apps/render.rs` — Apps（连接器）系统提示词渲染

## 功能概述

该文件负责将可用的 Apps（连接器）信息渲染为系统提示词片段，插入到发送给模型的上下文中。渲染的内容告知模型如何通过 `app://` URI 协议触发应用，以及应用与 MCP 工具的关系。仅当存在同时满足 `is_accessible` 和 `is_enabled` 条件的连接器时才会生成 Apps 段落。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `render_apps_section` | `fn(connectors: &[AppInfo]) -> Option<String>` | 检查是否存在可用且启用的连接器，有则生成带有 `APPS_INSTRUCTIONS_OPEN_TAG`/`APPS_INSTRUCTIONS_CLOSE_TAG` 包裹的 Apps 说明段落 |

## 依赖关系

- **引用的 crate**: `codex_app_server_protocol::AppInfo`、`codex_protocol::protocol`（标签常量）
- **引用的模块**: `crate::mcp::CODEX_APPS_MCP_SERVER_NAME`
- **被引用方**: 系统提示词构建流程

## 实现备注

渲染的提示词内容包含了对模型的关键指引：应用可以通过 `[$app-name](app://{connector_id})` 格式显式触发，也可以基于上下文隐式触发；应用等同于特定 MCP 服务器下的一组工具；可通过 `tool_search` 工具懒加载发现。测试覆盖了无可用连接器、仅可访问但未启用、仅启用但不可访问等边界情况。
