# `config_requirements.rs` — 配置需求约束系统

## 功能概述

实现 Codex 的配置需求（requirements）约束系统，用于从系统 `requirements.toml`、MDM 管理策略或云端需求中加载并强制执行安全策略约束。包括审批策略（approval policy）、沙箱策略（sandbox policy）、Web 搜索模式、功能特性开关、MCP 服务器需求、执行策略规则、数据驻留要求和网络约束等多维度约束。支持多来源需求的合并（高优先级优先，但 `false` 会向下传播），以及将需求 TOML 表示转换为内部带约束验证的 `ConfigRequirements` 类型。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `RequirementSource` | 枚举 | 需求来源（Unknown / MDM / 云端 / 系统文件 / 遗留配置） |
| `Sourced<T>` | 泛型结构体 | 将值与其来源关联，用于错误消息 |
| `ConstrainedWithSource<T>` | 泛型结构体 | 带来源信息的受约束值，包装 `Constrained<T>` |
| `ConfigRequirements` | 结构体 | 归一化后的配置需求，所有字段为受约束类型 |
| `ConfigRequirementsToml` | 结构体 | TOML 反序列化的原始需求表示 |
| `ConfigRequirementsWithSources` | 结构体 | 带来源信息的中间需求表示，支持多来源合并 |
| `SandboxModeRequirement` | 枚举 | 沙箱模式需求（ReadOnly / WorkspaceWrite / DangerFullAccess / ExternalSandbox） |
| `WebSearchModeRequirement` | 枚举 | Web 搜索模式需求（Disabled / Cached / Live） |
| `ResidencyRequirement` | 枚举 | 数据驻留要求（当前仅支持 Us） |
| `NetworkConstraints` | 结构体 | 归一化后的网络约束 |
| `NetworkDomainPermissionsToml` | 结构体 | 域名权限映射（allow/deny） |
| `NetworkUnixSocketPermissionsToml` | 结构体 | Unix 套接字权限映射（allow/none） |
| `FeatureRequirementsToml` | 结构体 | 功能特性开关需求 |
| `McpServerRequirement` / `McpServerIdentity` | 结构体/枚举 | MCP 服务器需求及其身份标识 |
| `AppsRequirementsToml` / `AppRequirementToml` | 结构体 | 应用启用/禁用需求 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `ConfigRequirementsWithSources::merge_unset_fields` | `(&mut self, source, other)` | 按优先级合并需求：仅填充 self 中尚未设置的字段 |
| `ConfigRequirementsWithSources::into_toml` | `(self) -> ConfigRequirementsToml` | 将带来源的需求转换回原始 TOML 表示 |
| `TryFrom<ConfigRequirementsWithSources> for ConfigRequirements` | trait 实现 | 将多来源需求转换为带约束验证的最终需求 |
| `merge_enablement_settings_descending` | `(base, incoming)` | 合并应用启用设置：`false` 优先于 `true`（任何来源的禁用都会生效） |
| `legacy_domain_permissions_from_lists` | `(allowed, denied) -> Option<...>` | 将遗留列表格式转换为统一的域名权限映射 |

## 依赖关系

- **引用的 crate**: `serde`、`codex_protocol`、`codex_utils_absolute_path`、`codex_execpolicy`
- **被引用方**: `codex-config` 的 `lib.rs` 重导出大量类型；`state.rs` 中 `ConfigLayerStack` 持有 `ConfigRequirements`

## 实现备注

- `TryFrom` 实现中，`allowed_web_search_modes` 始终隐式包含 `Disabled`，确保用户始终可以禁用 Web 搜索。
- `allowed_sandbox_modes` 必须包含 `read-only`，因为 ReadOnly 是默认策略且其他模式（WorkspaceWrite、ExternalSandbox）需要额外参数。
- `merge_unset_fields` 使用 `fill_missing_take!` 宏配合完整析构确保新增字段不会被遗漏。
- 内含大量单元测试覆盖合并逻辑、反序列化、约束验证和来源追踪。
