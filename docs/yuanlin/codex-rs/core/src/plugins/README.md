# plugins/ -- 插件系统

## 功能概述

`plugins/` 是 Codex 的插件管理系统，负责插件的发现、加载、安装、卸载、同步和渲染。插件可以为 Codex 添加额外的 MCP 服务器、应用连接器和自定义指令。该系统支持本地插件和远程插件市场，提供插件清单解析、启动同步、提及检测（@mentions）以及工具建议中的可发现插件推荐。

## 目录结构

```
plugins/
├── mod.rs                    # 模块入口，汇总导出所有公开类型
├── manager.rs                # PluginsManager：插件全生命周期管理（安装/卸载/同步/加载）
├── manifest.rs               # 插件清单解析（PluginManifestInterface、PluginManifestPaths）
├── store.rs                  # PluginStore：磁盘上的插件存储管理
├── marketplace.rs            # 市场接口（MarketplaceInterface、策略、来源）
├── remote.rs                 # 远程插件操作（fetch、enable、uninstall）
├── startup_sync.rs           # 启动时远程插件同步和 OpenAI 策划仓库同步
├── discoverable.rs           # 可发现插件列表（用于 tool_suggest）
├── injection.rs              # 插件指令注入构建
├── render.rs                 # 插件 section 渲染（用于 developer message）
├── mentions.rs               # 消息中的插件/连接器提及检测
├── toggles.rs                # 插件启用候选项收集
├── test_support.rs           # 测试辅助
├── discoverable_tests.rs     # 可发现插件测试
├── manager_tests.rs          # 管理器测试
├── marketplace_tests.rs      # 市场测试
├── mentions_tests.rs         # 提及检测测试
├── render_tests.rs           # 渲染测试
├── startup_sync_tests.rs     # 启动同步测试
└── store_tests.rs            # 存储测试
```

## 依赖关系

- **上游依赖**：`codex-plugin`（核心插件类型 `LoadedPlugin`、`PluginId`、`PluginCapabilitySummary`）、`codex-app-server-protocol`（AppInfo、ConfigValueWriteParams）、`codex-analytics`（分析事件）
- **内部依赖**：`config/`（Config、ConfigService、PluginConfig）、`config_loader/`（ConfigLayerStack）、`auth/`（AuthManager）、`loader`（技能加载）
- **被依赖方**：`mcp/`（McpManager 持有 PluginsManager）、`state/`（SessionServices 持有 PluginsManager）、`tools/`（可发现插件用于工具建议）

## 核心接口/API

| 接口/类型 | 说明 |
|---|---|
| `PluginsManager` | 插件管理器：`install()`、`uninstall()`、`plugins_for_config()`、`sync_remote()` |
| `LoadedPlugin` | 已加载插件，包含 MCP 服务器和应用连接器配置 |
| `PluginCapabilitySummary` | 插件能力摘要（display_name、app_connector_ids、mcp_server_names） |
| `PluginManifestInterface` | 插件清单接口（解析 manifest.json） |
| `ConfiguredMarketplace` | 已配置的市场实例 |
| `render_plugins_section()` | 渲染插件描述用于 developer message |
| `build_plugin_injections()` | 构建插件指令注入内容 |
| `list_tool_suggest_discoverable_plugins()` | 列出可用于工具建议的可发现插件 |
