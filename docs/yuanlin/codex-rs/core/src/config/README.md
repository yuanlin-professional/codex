# config/ -- 配置系统

## 功能概述

`config/` 是 Codex 的核心配置模块，负责从多个来源（全局配置文件、项目配置、CLI 标志、环境变量、云端需求层等）加载、合并和管理配置。`Config` 结构体是整个 Codex 运行时的统一配置源，涵盖模型选择、沙箱策略、审批策略、MCP 服务器、插件、网络代理、OTel 遥测、Shell 环境等所有可配置维度。配置支持热编辑和动态更新。

## 目录结构

```
config/
├── mod.rs                        # Config 结构体定义和主要构建逻辑（约 1200 行）
├── types.rs                      # 配置类型定义（McpServerConfig、OtelConfig、MemoriesConfig 等）
├── permissions.rs                # 权限配置（PermissionsToml、文件系统/网络权限解析）
├── schema.rs                     # 配置 JSON Schema 生成
├── schema.md                     # 配置 Schema 文档
├── edit.rs                       # 配置编辑器（ConfigEdit、ConfigEditsBuilder）
├── profile.rs                    # 配置 Profile 管理
├── service.rs                    # ConfigService：运行时配置服务
├── agent_roles.rs                # 代理角色配置解析
├── managed_features.rs           # 受管理的功能特性
├── network_proxy_spec.rs         # 网络代理规格
├── config_tests.rs               # 配置测试
├── types_tests.rs                # 类型测试
├── edit_tests.rs                 # 编辑器测试
├── permissions_tests.rs          # 权限测试
├── schema_tests.rs               # Schema 测试
├── service_tests.rs              # 服务测试
└── network_proxy_spec_tests.rs   # 网络代理测试
```

## 依赖关系

- **上游依赖**：`codex-protocol`（协议配置类型 `SandboxPolicy`、`AskForApproval`、`SandboxMode` 等）、`codex-features`（功能标志 `Feature`、`Features`）、`codex-config`（配置层栈基础设施）、`codex-app-server-protocol`（app server 配置类型）
- **内部依赖**：`config_loader/`（配置层加载）、`instructions/`（用户指令）、`model_provider_info`（模型提供商信息）、`unified_exec/`（后台终端超时默认值）
- **被依赖方**：几乎所有核心模块（`codex/`、`tools/`、`tasks/`、`mcp/`、`models_manager/`、`plugins/`、`sandboxing/`、`agent/` 等）

## 核心接口/API

| 接口/类型 | 说明 |
|---|---|
| `Config` | 核心配置结构体，包含模型、沙箱、审批、MCP、插件等所有配置 |
| `ConfigBuilder` | 配置构建器，支持从各种来源组装 Config |
| `ConfigOverrides` | 配置覆盖参数（用于 CLI 标志等运行时覆盖） |
| `ConfigService` | 运行时配置服务，支持热编辑和实时更新 |
| `ConfigEdit` / `ConfigEditsBuilder` | 配置编辑操作和批量编辑构建器 |
| `McpServerConfig` | MCP 服务器配置（传输方式、超时、工具过滤等） |
| `PermissionsToml` | 权限配置 TOML 结构 |
| `Features` | 功能标志集合 |
