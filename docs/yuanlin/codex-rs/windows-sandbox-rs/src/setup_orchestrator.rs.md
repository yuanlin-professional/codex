# `setup_orchestrator.rs` -- Windows 沙箱设置编排器

## 功能概述

本模块是 Windows 沙箱环境配置的编排器。它负责确定需要的读/写根目录列表，构建提权载荷（ElevationPayload），通过 `ShellExecuteExW` 以 UAC 提权方式启动设置辅助程序，或在已提权环境下直接运行。还负责管理离线沙箱用户的代理端口和防火墙设置，以及过滤敏感写入根目录（如 `.sandbox` 等）。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `SandboxSetupRequest` | struct | 设置请求参数：策略、工作目录、环境变量、codex_home、代理状态 |
| `SetupRootOverrides` | struct | 可选的读/写根目录覆盖 |
| `SetupMarker` | struct | 设置完成标记文件内容：版本、用户名、代理端口等 |
| `SandboxUsersFile` | struct | 沙箱用户文件：离线和在线用户的加密凭据 |
| `ElevationPayload` | struct (Serialize) | 传递给提权辅助程序的 Base64 编码 JSON 载荷 |
| `SandboxNetworkIdentity` | enum | 网络身份：`Offline`（无网络）或 `Online`（有网络） |
| `OfflineProxySettings` | struct | 离线代理设置：允许的端口和本地绑定标志 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `run_setup_refresh` | `pub fn run_setup_refresh(...)` | 非提权设置刷新入口 |
| `run_elevated_setup` | `pub fn run_elevated_setup(request, overrides) -> Result<()>` | 提权设置入口，通过 UAC 运行辅助程序 |
| `gather_read_roots` | `pub fn gather_read_roots(cwd, policy, codex_home) -> Vec<PathBuf>` | 收集读取根目录列表（区分完整/受限读取） |
| `gather_write_roots` | `pub fn gather_write_roots(policy, policy_cwd, command_cwd, env_map) -> Vec<PathBuf>` | 收集写入根目录列表 |
| `offline_proxy_settings_from_env` | `pub fn offline_proxy_settings_from_env(env_map, identity) -> OfflineProxySettings` | 从环境变量提取离线代理设置 |
| `filter_sensitive_write_roots` | `fn filter_sensitive_write_roots(roots, codex_home) -> Vec<PathBuf>` | 过滤掉沙箱控制目录以防止篡改 |

## 依赖关系

- **引用的 crate**: `windows_sys`, `anyhow`, `base64`, `serde`, `serde_json`
- **被引用方**: `lib.rs`（导出为 `run_elevated_setup` 等公开 API）

## 实现备注

- 使用 `ShellExecuteExW` + `runas` 动词实现 UAC 提权
- 用户配置文件读取根目录排除了 `.ssh`、`.gnupg`、`.aws` 等敏感目录
- `USERPROFILE_READ_ROOT_EXCLUSIONS` 以大小写不敏感方式匹配
- 设置标记文件用于检测设置版本和代理端口变更
