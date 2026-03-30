# `types.rs` -- 配置类型定义集合

## 功能概述
集中定义了 `Config` 所使用的各种辅助结构体和枚举类型。本文件遵循"仅定义类型、不含业务逻辑"的原则（少数 `From` 实现除外），涵盖 MCP 服务器配置、App/连接器配置、TUI 配置、OTEL 配置、Memories 配置、Shell 环境策略、通知配置、历史记录配置等全部配置子域。

## 关键结构体/枚举
| 名称 | 类型 | 说明 |
|------|------|------|
| `McpServerConfig` | struct | MCP 服务器完整配置，包含传输方式、启用状态、超时、工具过滤等 |
| `McpServerTransportConfig` | enum | MCP 传输配置：`Stdio`（命令行）或 `StreamableHttp`（HTTP） |
| `RawMcpServerConfig` | struct | MCP 服务器的原始反序列化结构（用于自定义 Deserialize） |
| `McpServerToolConfig` | struct | 单个 MCP 工具的审批配置 |
| `AppConfig` | struct | 单个 App/连接器的配置（启用、destructive/open_world 控制、工具审批等） |
| `AppsConfigToml` | struct | `[apps]` 表的完整配置，含默认值和按 ID 索引的 App 配置 |
| `Tui` | struct | TUI 配置（通知、动画、tooltip、alternate screen、status line、theme 等） |
| `Notice` | struct | 用户通知确认状态（full access warning、rate limit nudge、model migration 等） |
| `OtelConfigToml` / `OtelConfig` | struct | OTEL 配置的 TOML 和运行时形式 |
| `MemoriesToml` / `MemoriesConfig` | struct | Memories 配置的 TOML 和运行时形式（含默认值） |
| `ShellEnvironmentPolicyToml` / `ShellEnvironmentPolicy` | struct | Shell 环境策略的 TOML 和运行时形式 |
| `SandboxWorkspaceWrite` | struct | workspace-write sandbox 模式的配置 |
| `History` | struct | 历史记录配置（持久化模式、最大大小） |
| `UriBasedFileOpener` | enum | URI 编辑器方案：VSCode、VSCode Insiders、Windsurf、Cursor、None |
| `WindowsToml` | struct | Windows 特定配置（sandbox 模式、private desktop） |
| `AppToolApproval` | enum | 工具审批模式：`Auto`、`Prompt`、`Approve` |

## 关键函数/方法
| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `McpServerConfig::deserialize` | 自定义 Deserialize | 处理 stdio/http 传输类型推断、字段互斥校验、超时解析 |
| `MemoriesConfig::from(MemoriesToml)` | From trait | 从 TOML 配置构造运行时配置，应用默认值和 clamp 约束 |
| `ShellEnvironmentPolicy::from(ShellEnvironmentPolicyToml)` | From trait | 从 TOML 配置构造运行时策略，编译 wildcard 模式 |
| `UriBasedFileOpener::get_scheme` | `fn(&self) -> Option<&str>` | 返回编辑器的 URI scheme |

## 依赖关系
- **引用的 crate**: `serde`, `schemars`, `codex_protocol`, `codex_config`, `codex_utils_absolute_path`, `wildmatch`
- **被引用方**: `config` 模块内几乎所有文件

## 实现备注
- `McpServerConfig` 的自定义 `Deserialize` 实现先反序列化为 `RawMcpServerConfig`，然后根据 `command`/`url` 字段推断传输类型，并校验字段间的互斥约束（例如 stdio 不允许设置 `url`）。
- `MemoriesConfig` 对多个数值字段应用 `clamp` 约束（如 `max_rollout_age_days` 限制在 0-90 之间）。
- `option_duration_secs` 模块提供 `Duration` 和 `f64` 之间的 serde 转换。
- `McpServerDisabledReason` 记录服务器被禁用的原因来源（未知 vs 管理员需求）。
