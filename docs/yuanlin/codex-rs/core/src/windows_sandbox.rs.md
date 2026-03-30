# `windows_sandbox.rs` — Windows 沙箱配置与安装管理

## 功能概述

该文件封装了 Windows 平台下沙箱的配置解析、安装流程和指标上报逻辑。它定义了 Windows 沙箱的两个级别（Elevated 和 Unelevated/RestrictedToken），从配置文件和 feature flags 中解析沙箱级别，管理沙箱的首次安装（elevated setup）和遗留预检（legacy preflight），安装完成后将配置持久化到 `config.toml`，并通过 `codex_otel` 上报安装成功/失败的指标。非 Windows 平台上的函数均提供 stub 实现。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `WindowsSandboxSetupMode` | enum | 沙箱安装模式：Elevated（提权安装）/ Unelevated（非提权安装） |
| `WindowsSandboxSetupRequest` | struct | 沙箱安装请求：安装模式、沙箱策略、工作目录、环境变量等 |
| `WindowsSandboxLevelExt` | trait | `WindowsSandboxLevel` 的扩展 trait，提供从配置和 features 解析的方法 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `WindowsSandboxLevel::from_config` | `fn(config: &Config) -> WindowsSandboxLevel` | 从配置中解析沙箱级别 |
| `WindowsSandboxLevel::from_features` | `fn(features: &Features) -> WindowsSandboxLevel` | 从 feature flags 解析沙箱级别 |
| `resolve_windows_sandbox_mode` | `fn(cfg, profile) -> Option<WindowsSandboxModeToml>` | 综合配置和遗留 features 解析沙箱模式 |
| `run_windows_sandbox_setup` | `async fn(request) -> Result<()>` | 执行沙箱安装的入口函数，含指标上报 |
| `run_windows_sandbox_setup_and_persist` | `async fn(request) -> Result<()>` | 安装并持久化沙箱配置（通过 `ConfigEditsBuilder`） |
| `run_elevated_setup` | `fn(policy, ...) -> Result<()>` | 执行提权安装（仅 Windows） |
| `run_legacy_setup_preflight` | `fn(policy, ...) -> Result<()>` | 执行遗留预检（仅 Windows） |
| `sandbox_setup_is_complete` | `fn(codex_home: &Path) -> bool` | 检查沙箱安装是否已完成 |
| `legacy_windows_sandbox_mode` | `fn(features) -> Option<WindowsSandboxModeToml>` | 从遗留 feature 键解析沙箱模式 |

## 依赖关系

- **引用的 crate**: `codex_features`（Feature flag 系统）、`codex_protocol`（`WindowsSandboxLevel`）、`codex_otel`（指标上报）、`codex_windows_sandbox`（实际沙箱操作，仅 Windows）
- **被引用方**: TUI/CLI 层的沙箱 NUX（新用户体验）流程、会话初始化

## 实现备注

- 使用 `#[cfg(target_os = "windows")]` 和 `#[cfg(not(target_os = "windows"))]` 双重实现，非 Windows 平台上所有操作函数返回 `bail!`。
- 遗留 feature flags（`windows_sandbox_elevated`、`windows_sandbox`、`enable_experimental_windows_sandbox`）会被自动迁移到新的 `[windows]` 配置节。
- `ELEVATED_SANDBOX_NUX_ENABLED` 常量作为"杀死开关"，可在代码层面快速回退到旧版沙箱 NUX 流程。
- 沙箱安装在 `spawn_blocking` 中执行以避免阻塞 async 运行时。
- 安装成功后通过 `ConfigEditsBuilder` 将 `windows.sandbox` 设置持久化，并清理遗留 feature keys。
- 指标上报包含安装模式（elevated/unelevated）、发起者标签、耗时，以及失败时的错误代码和消息。
