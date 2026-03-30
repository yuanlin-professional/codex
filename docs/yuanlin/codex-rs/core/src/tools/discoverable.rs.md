# `discoverable.rs` — 可发现工具定义

## 功能概述
定义了可发现工具（Discoverable Tool）的类型系统，用于工具建议功能（tool_suggest）。支持两种可发现工具类型：Connector（应用连接器）和 Plugin（插件），提供统一的访问接口和客户端过滤逻辑。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `DiscoverableToolType` | enum | 工具类型：Connector / Plugin |
| `DiscoverableToolAction` | enum | 建议动作：Install / Enable |
| `DiscoverableTool` | enum | 可发现工具：Connector(AppInfo) / Plugin(DiscoverablePluginInfo) |
| `DiscoverablePluginInfo` | struct | 插件信息：id、name、description、has_skills、mcp_server_names、app_connector_ids |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `filter_tool_suggest_discoverable_tools_for_client` | `fn(discoverable_tools, app_server_client_name) -> Vec<DiscoverableTool>` | 为 TUI 客户端过滤掉 Plugin 类型的工具 |
| `DiscoverableTool::id/name/description/install_url` | 访问器方法 | 统一访问 Connector 和 Plugin 的属性 |

## 依赖关系
- **引用的 crate**: `codex_app_server_protocol`, `serde`
- **内部依赖**: `crate::plugins::PluginCapabilitySummary`
- **被引用方**: `spec.rs` 中的 `create_tool_suggest_tool`, `router.rs` 中的 `ToolRouterParams`

## 实现备注
- TUI 客户端（`codex-tui`）不显示 Plugin 类型工具，仅显示 Connector
- `DiscoverablePluginInfo` 从 `PluginCapabilitySummary` 转换而来
