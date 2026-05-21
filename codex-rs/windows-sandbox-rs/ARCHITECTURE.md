# codex-rs/windows-sandbox-rs 架构文档

## 1. 概述

`codex-windows-sandbox` 是 Codex 在 Windows 平台上的沙箱隔离 crate，负责通过 Windows 受限令牌（Restricted Token）、能力 SID（Capability SID）、ACL 权限控制、WFP 网络过滤和 ConPTY 伪终端等 OS 原语，将子进程限制在声明式安全策略所允许的文件系统和网络访问范围内。它同时提供 legacy（直接受限令牌产生）和 elevated（通过 IPC 命令运行器以独立沙箱用户产生）两种后端，以及一个一次性 UAC 提权 setup 流程来创建沙箱用户、配置防火墙和 ACL。

规模：~50 源文件、~8,000 行代码。

---

## 2. 功能模块索引

| 模块标签 | 职责一句话 | 包含文件/目录 |
|----------|-----------|--------------|
| `[策略解析]` | 解析和校验沙箱策略（SandboxPolicy）及权限配置 | `policy.rs`, `resolved_permissions.rs` |
| `[令牌构造]` | 创建 Windows 受限令牌（ReadOnly / WritableRoots），管理能力 SID | `token.rs`, `cap.rs` |
| `[ACL管理]` | 操纵文件系统 DACL，授予/拒绝读写权限 | `acl.rs`, `allow.rs`, `deny_read_acl.rs`, `deny_read_state.rs`, `deny_read_resolver.rs`, `workspace_acl.rs`, `audit.rs` |
| `[网络隔离]` | 通过 WFP（Windows Filtering Platform）按用户 SID 阻断网络 | `wfp.rs`, `wfp/filter_specs.rs`, `wfp_setup.rs` |
| `[进程产生]` | 以受限令牌创建子进程（pipe/ConPTY 两种 stdio 模式） | `process.rs`, `conpty/mod.rs`, `proc_thread_attr.rs`, `spawn_prep.rs` |
| `[桌面隔离]` | 创建独立 Windows Desktop 防止 shatter 攻击 | `desktop.rs` |
| `[Setup编排]` | 一次性提权 setup 流程：创建沙箱用户、设置 ACL 和防火墙 | `setup.rs`, `setup_error.rs`, `identity.rs`, `hide_users.rs` |
| `[Elevated后端]` | 通过 IPC 管道与 command-runner 子进程通信的提权沙箱后端 | `elevated/`, `elevated_impl.rs`, `unified_exec/` |
| `[IPC协议]` | 帧协议编解码、消息类型定义 | `elevated/ipc_framed.rs`, `elevated/runner_pipe.rs`, `elevated/runner_client.rs` |
| `[环境工具]` | 路径规范化、环境变量构建、日志、DPAPI 加解密等辅助 | `env.rs`, `path_normalization.rs`, `logging.rs`, `dpapi.rs`, `winutil.rs`, `sandbox_utils.rs`, `helper_materialization.rs`, `ssh_config_dependencies.rs` |
| `[二进制入口]` | setup helper 和 command-runner 两个独立可执行文件 | `bin/setup_main/`, `bin/command_runner/` |

---

## 3. 逐目录逐文件说明

### 3.1 顶层文件 (src/*.rs)

**lib.rs** `[策略解析]` — crate 根入口，声明所有子模块（大部分仅 `#[cfg(target_os = "windows")]`）并通过 `pub use` 导出公共 API。非 Windows 平台提供 `stub` 模块返回错误。
- 内嵌 `windows_impl` 模块：包含 `CaptureResult` struct 和 `run_windows_sandbox_capture()` / `run_windows_sandbox_capture_with_filesystem_overrides()` / `run_windows_sandbox_legacy_preflight()` 函数

**policy.rs** `[策略解析]` — 解析沙箱策略字符串为 `SandboxPolicy` enum。
- `parse_policy(value: &str) -> Result<SandboxPolicy>` fn：支持 `"read-only"` / `"workspace-write"` 预设和 JSON 格式
- 拒绝 `DangerFullAccess` 和 `ExternalSandbox`

**resolved_permissions.rs** `[策略解析]` — 将高层策略/权限配置解析为 Windows 沙箱可执行的运行时权限视图。
- `ResolvedWindowsSandboxPermissions` struct：持有 `FileSystemSandboxPolicy` + `NetworkSandboxPolicy`
- `WindowsSandboxTokenMode` enum：`ReadOnlyCapability` / `WritableRootsCapability`
- `token_mode_for_permission_profile()` fn：为 managed PermissionProfile 选择令牌族
- 方法：`from_legacy_policy_for_cwd()`、`try_from_permission_profile()`、`writable_roots_for_cwd()`、`readable_roots_for_cwd()`、`has_full_disk_read_access()`、`should_apply_network_block()`

**token.rs** `[令牌构造]` — Windows 受限令牌创建核心。
- `LocalSid` struct：堆分配 SID 包装
- `get_current_token_for_restriction()` fn：获取当前进程令牌用于限制
- `create_readonly_token_with_cap_from()` / `create_readonly_token_with_caps_from()` / `create_readonly_token_with_caps_and_user_from()` fn：创建只读受限令牌
- `create_workspace_write_token_with_caps_from()` / `create_workspace_write_token_with_caps_and_user_from()` fn：创建可写受限令牌（WRITE_RESTRICTED）
- `convert_string_sid_to_sid()` fn

**cap.rs** `[令牌构造]` — 能力 SID 管理，为工作目录和可写根创建/加载 Capability SID。
- `load_or_create_cap_sids()` fn：从磁盘加载或新建 cap SIDs
- `workspace_cap_sid_for_cwd()` fn / `workspace_write_cap_sid_for_root()` fn / `workspace_write_root_contains_path()` fn / `workspace_write_root_overlaps_path()` fn

**acl.rs** `[ACL管理]` — 底层 DACL 操纵函数。
- `add_deny_read_ace()` / `add_deny_write_ace()` fn：添加拒绝 ACE
- `ensure_allow_mask_aces()` / `ensure_allow_mask_aces_with_inheritance()` / `ensure_allow_write_aces()` fn：确保允许 ACE 存在
- `allow_null_device()` fn：为 NUL 设备添加允许 ACE
- `fetch_dacl_handle()` fn / `path_mask_allows()` fn

**allow.rs** `[ACL管理]` — 计算可写根下需要保护的子路径（如 `.git`、`.codex`）。
- `AllowDenyPaths` struct：`allow` + `deny` 路径集
- `compute_allow_paths_for_permissions()` fn

**deny_read_acl.rs** `[ACL管理]` — 批量应用 deny-read ACL。
- `plan_deny_read_acl_paths()` fn：规划需要 deny-read 的路径列表
- `apply_deny_read_acls()` fn：实际应用 ACL

**deny_read_state.rs** `[ACL管理]` — 持久化 deny-read ACL 状态，跨会话同步。
- `sync_persistent_deny_read_acls()` fn

**deny_read_resolver.rs** `[ACL管理]` — 将 Codex 文件系统策略中的 deny-read 条目（含 glob）解析为具体 Windows 路径。
- `resolve_windows_deny_read_paths()` fn：支持精确路径和 glob 模式展开
- 跨平台可用（非 Windows 上也编译）

**workspace_acl.rs** `[ACL管理]` — 工作区 ACL 辅助。
- `is_command_cwd_root()` fn

**audit.rs** `[ACL管理]` — 世界可写路径扫描与拒绝 ACE 应用。
- `apply_world_writable_scan_and_denies()` fn
- `apply_world_writable_scan_and_denies_for_permissions()` fn

**process.rs** `[进程产生]` — 以受限令牌创建子进程（CreateProcessAsUser），支持 pipe stdio。
- `PipeSpawnHandles` struct：进程 + pipe 句柄
- `StdinMode` enum / `StderrMode` enum
- `create_process_as_user()` fn / `spawn_process_with_pipes()` fn / `read_handle_loop()` fn

**proc_thread_attr.rs** `[进程产生]` — `PROC_THREAD_ATTRIBUTE_LIST` 封装，用于传递 ConPTY/Desktop 属性。
- `ProcThreadAttributeList` struct

**spawn_prep.rs** `[进程产生]` — Legacy 后端的产生前准备：解析策略、构建令牌、应用 ACL。
- `SpawnPrepOptions` struct / `LegacyAclSids` struct
- `prepare_legacy_spawn_context()` fn / `prepare_legacy_session_security()` fn
- `apply_legacy_session_acl_rules()` fn / `legacy_session_capability_roots()` fn

**desktop.rs** `[桌面隔离]` — 创建隔离 Windows Desktop。
- `LaunchDesktop` struct：RAII 封装，Drop 时关闭 desktop

**wfp.rs** `[网络隔离]` — WFP 过滤器管理（provider、sublayer、filter CRUD）。
- `install_wfp_filters_for_account()` fn：为指定用户安装阻断过滤器

**wfp/filter_specs.rs** `[网络隔离]` — WFP 过滤器规格常量（GUID、名称、权重）。

**wfp_setup.rs** `[网络隔离]` — Setup 阶段安装 WFP 过滤器的高层接口。
- `install_wfp_filters()` fn

**setup.rs** `[Setup编排]` — 沙箱 setup 编排主逻辑：构建 payload、调用提权 helper、管理 read/write roots。
- `SETUP_VERSION` 常量（当前 v5）
- `SandboxSetupRequest` struct / `SetupRootOverrides` struct
- `SetupMarker` struct / `SandboxUsersFile` struct / `SandboxUserRecord` struct
- `run_elevated_setup()` fn / `run_setup_refresh()` fn / `run_setup_refresh_with_extra_read_roots()` fn
- `sandbox_dir()` / `sandbox_bin_dir()` / `sandbox_secrets_dir()` fn
- 内部：`ElevationPayload`、proxy 设置解析、敏感路径过滤

**setup_error.rs** `[Setup编排]` — Setup 错误类型与错误报告文件管理。
- `SetupErrorCode` enum / `SetupErrorReport` struct / `SetupFailure` struct
- `write_setup_error_report()` / `setup_error_path()` / `extract_failure()` / `sanitize_setup_metric_tag_value()` fn

**identity.rs** `[Setup编排]` — 沙箱用户凭据管理（加载、解密、验证 setup 状态）。
- `SandboxCreds` struct：`username` + `password`
- `require_logon_sandbox_creds()` fn：确保沙箱凭据就绪，必要时触发 setup
- `sandbox_setup_is_complete()` fn

**hide_users.rs** `[Setup编排]` — 从 Windows 登录界面隐藏沙箱用户。
- `hide_newly_created_users()` fn / `hide_current_user_profile_dir()` fn

**elevated_impl.rs** `[Elevated后端]` — Elevated 后端的捕获模式入口（非 unified-exec 的一次性执行）。
- `ElevatedSandboxCaptureRequest` struct / `ElevatedSandboxProfileCaptureRequest` struct
- `run_windows_sandbox_capture()` fn（elevated 路径）
- `run_windows_sandbox_capture_for_permission_profile()` fn

**env.rs** `[环境工具]` — 环境变量处理（PATH 继承、PAGER 抑制、NUL 设备规范化）。

**path_normalization.rs** `[环境工具]` — 路径规范化辅助（`canonicalize_path()`、大小写折叠）。

**logging.rs** `[环境工具]` — 沙箱操作日志（成功/失败/note 写入日志文件）。
- `LOG_FILE_NAME` 常量 / `log_note()` / `log_success()` / `log_failure()` fn

**dpapi.rs** `[环境工具]` — Windows DPAPI 数据保护（加密/解密沙箱用户密码）。
- `protect()` / `unprotect()` fn

**winutil.rs** `[环境工具]` — Windows 通用工具函数。
- `to_wide()` fn：字符串转宽字符
- `quote_windows_arg()` fn：命令行参数转义
- `string_from_sid_bytes()` fn

**sandbox_utils.rs** `[环境工具]` — 杂项沙箱辅助函数。

**helper_materialization.rs** `[环境工具]` — Helper 二进制查找与释放逻辑。
- `resolve_current_exe_for_launch()` fn / `bundled_executable_path_for_exe()` fn / `helper_bin_dir()` fn

**ssh_config_dependencies.rs** `[环境工具]` — 解析 SSH config 引用的文件路径，确保它们不被错误地暴露给沙箱。
- `ssh_config_dependency_paths()` fn
- 跨平台可用（test 时也编译）

---

### 3.2 conpty/

ConPTY 伪终端产生。

**conpty/mod.rs** `[进程产生]` — 封装 ConPTY 创建和以受限令牌 + PTY 产生进程。
- `ConptyInstance` struct：持有 pseudoconsole 句柄、input/output pipe 句柄
- `spawn_conpty_process_as_user()` fn

---

### 3.3 elevated/

Elevated 后端 IPC 基础设施。

**elevated/mod.rs** `[IPC协议]` — 模块入口，导出 `ipc_framed`、`runner_client`、`runner_pipe`。

**elevated/ipc_framed.rs** `[IPC协议]` — 帧协议定义与编解码。
- `IPC_PROTOCOL_VERSION` 常量
- `Message` enum：`Spawn(SpawnRequest)` / `Output(OutputPayload)` / `Exit(ExitPayload)` / `Error(ErrorPayload)` / `SpawnReady(SpawnReady)` / `Stdin(Vec<u8>)` / `Terminate` / `Resize(ResizePayload)`
- `FramedMessage` struct / `OutputStream` enum
- `read_frame()` / `write_frame()` / `encode_bytes()` / `decode_bytes()` fn

**elevated/runner_pipe.rs** `[IPC协议]` — 命名管道创建与连接辅助。
- `create_named_pipe()` / `connect_pipe()` / `pipe_pair()` / `find_runner_exe()` fn

**elevated/runner_client.rs** `[IPC协议]` — 从宿主侧连接 command-runner 的客户端逻辑。
- 产生 command-runner 进程、建立 IPC 管道连接、发送 SpawnRequest、接收输出

---

### 3.4 unified_exec/

Unified exec 会话产生（长生命周期交互式进程）。

**unified_exec/mod.rs** `[Elevated后端]` — 模块入口与公共 API。
- `spawn_windows_sandbox_session_legacy()` async fn：legacy 后端产生会话
- `spawn_windows_sandbox_session_elevated()` async fn：elevated 后端产生会话
- `spawn_windows_sandbox_session_elevated_for_permission_profile()` async fn：managed 权限配置后端

**unified_exec/backends/mod.rs** `[Elevated后端]` — 后端模块入口。

**unified_exec/backends/legacy.rs** `[Elevated后端]` — Legacy 后端实现：直接受限令牌 + ConPTY/pipe 产生。

**unified_exec/backends/elevated.rs** `[Elevated后端]` — Elevated 后端实现：通过 IPC 与 command-runner 通信。

**unified_exec/backends/windows_common.rs** `[Elevated后端]` — 两个后端共享的 Windows 辅助逻辑。

**unified_exec/tests.rs** `[Elevated后端]` — 集成测试。

---

### 3.5 bin/setup_main/

独立可执行文件 `codex-windows-sandbox-setup`：以提权方式运行，执行一次性沙箱环境准备。

**bin/setup_main/main.rs** `[二进制入口]` — 入口，Windows-only 分发到 `win::main()`。

**bin/setup_main/win.rs** `[二进制入口]` — Setup 主逻辑：解码 payload → 创建用户 → 配置 ACL → 安装 WFP 过滤器 → 写 marker。

**bin/setup_main/win/firewall.rs** `[二进制入口]` — COM 防火墙规则配置。

**bin/setup_main/win/read_acl_mutex.rs** `[二进制入口]` — 读 ACL 操作的互斥同步。

**bin/setup_main/win/sandbox_users.rs** `[二进制入口]` — 沙箱用户（offline/online）创建和密码管理。

**bin/setup_main/win/setup_runtime_bin.rs** `[二进制入口]` — 运行时二进制释放到 sandbox-bin 目录。

---

### 3.6 bin/command_runner/

独立可执行文件 `codex-command-runner`：由 elevated 后端以沙箱用户身份产生，作为 IPC 命令执行代理。

**bin/command_runner/main.rs** `[二进制入口]` — 入口，Windows-only 分发到 `win::main()`。

**bin/command_runner/win.rs** `[二进制入口]` — 运行器主循环：读取 SpawnRequest → 派生受限令牌 → 通过 ConPTY 或 pipe 产生子进程 → 流式转发 stdout/stderr → 发送 Exit 帧。

**bin/command_runner/win/cwd_junction.rs** `[二进制入口]` — 工作目录 junction（符号链接）创建，用于规避路径权限约束。

---

## 4. 模块间调用关系

```
Setup编排 (setup.rs / identity.rs)
├── 调用 → 策略解析 (resolved_permissions)
├── 调用 → ACL管理 (allow, deny_read_acl, workspace_acl)
├── 调用 → 网络隔离 (wfp_setup)
├── 调用 → 环境工具 (path_normalization, logging, dpapi, helper_materialization)
└── 产生 → bin/setup_main (提权 helper 进程)

Legacy 捕获入口 (windows_impl in lib.rs)
├── 调用 → 策略解析 (policy, resolved_permissions)
├── 调用 → 进程产生 (spawn_prep → token, cap, acl)
├── 调用 → 进程产生 (process::create_process_as_user)
└── 调用 → 环境工具 (logging)

Elevated 捕获入口 (elevated_impl.rs)
├── 调用 → 策略解析 (policy, resolved_permissions)
├── 调用 → Setup编排 (identity::require_logon_sandbox_creds)
├── 调用 → IPC协议 (runner_client → runner_pipe, ipc_framed)
└── 产生 → bin/command_runner (沙箱用户子进程)

Unified Exec 会话 (unified_exec/)
├── backends/legacy → 进程产生 (spawn_prep, conpty, process)
├── backends/elevated → IPC协议 (runner_client)
└── 两者 → 策略解析 (policy, resolved_permissions)

bin/command_runner
├── 调用 → 令牌构造 (token, cap)
├── 调用 → 进程产生 (conpty, process)
├── 调用 → ACL管理 (acl)
└── 使用 → IPC协议 (ipc_framed)

bin/setup_main
├── 调用 → ACL管理 (acl, deny_read_acl)
├── 调用 → 网络隔离 (wfp_setup)
├── 调用 → 令牌构造 (cap)
└── 调用 → 环境工具 (logging, winutil)
```

---

## 5. 外部 crate 依赖

### 5.1 Workspace 内部 crate

| Crate | 用途 |
|-------|------|
| `codex-protocol` | SandboxPolicy enum、PermissionProfile、FileSystemSandboxPolicy 等协议类型 |
| `codex-utils-pty` | ConPTY 抽象（`PsuedoCon`、`RawConPty`、`SpawnedProcess`） |
| `codex-utils-absolute-path` | `AbsolutePathBuf` 绝对路径类型 |
| `codex-utils-string` | 字符串工具 |
| `codex-otel` | OpenTelemetry / Statsig metrics 设置 |

### 5.2 第三方 crate

| Crate | 用途 |
|-------|------|
| `windows-sys` | Windows API 绑定（Security、Threading、Pipes、WFP、FileSystem 等） |
| `windows` | Windows COM API 绑定（Firewall COM 接口） |
| `anyhow` | 错误处理 |
| `serde` + `serde_json` | 序列化（Setup payload、marker 文件、IPC 消息） |
| `tokio` | 异步运行时（sync、rt 特性，用于 unified_exec） |
| `base64` | Base64 编解码（Setup payload 传输） |
| `chrono` | 日期/时间（Setup marker 时间戳） |
| `tempfile` | 临时文件/目录 |
| `rand` | 随机数（Desktop 名称生成等） |
| `dunce` | 路径规范化（去除 `\\?\` 前缀） |
| `glob` | 文件 glob 匹配（deny-read 路径展开） |
| `dirs-next` | 平台目录查找 |
