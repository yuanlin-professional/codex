# windows-sandbox-rs

## 功能概述

`codex-windows-sandbox` 是 Codex 在 Windows 平台上的沙箱隔离实现。它使用 Windows 原生安全机制来创建受限的执行环境，包括：

- **受限令牌 (Restricted Tokens)**：创建功能受限的 Windows 安全令牌来运行子进程
- **ACL 控制**：通过 DACL (Discretionary Access Control Lists) 控制文件系统读写权限
- **Capability SID**：使用自定义 SID 标识沙箱进程的权限范围
- **网络隔离**：通过环境变量配置阻止网络访问
- **桌面隔离**：支持在独立的 Windows Desktop 中运行进程
- **DPAPI 保护**：数据保护 API 用于凭据加密存储

该 crate 通过条件编译在 Windows 上提供完整功能，在非 Windows 平台上提供 stub 实现（返回错误）。

## 架构说明

```
                +-------------------------+
                | codex-windows-sandbox   |
                +------------+------------+
                             |
        +--------------------+--------------------+
        |            |            |               |
        v            v            v               v
  +-----+-----+ +---+----+ +----+-----+  +------+------+
  |   policy   | |  acl   | |  token   |  |   process   |
  |(沙箱策略解析)| |(ACL 管理)| |(令牌创建)|  |(进程创建)    |
  +-----------+ +--------+ +----------+  +-------------+
        |            |            |               |
        v            v            v               v
  +-----+-----+ +---+----+ +----+-----+  +------+------+
  |   allow    | | audit  | |  cap     |  |   conpty    |
  |(路径白名单) | |(安全审计)| |(SID 管理)|  |(伪终端)     |
  +-----------+ +--------+ +----------+  +-------------+

  其他模块:
    identity        - 沙箱用户身份管理
    desktop         - Windows Desktop 隔离
    dpapi           - 数据保护 API 加密/解密
    env             - 环境变量处理（网络阻断等）
    logging         - 沙箱操作日志
    setup           - 沙箱环境初始化编排
    workspace_acl   - 工作区 ACL 保护
    hide_users      - 隐藏沙箱用户
    helper_materialization - 辅助二进制部署
    path_normalization     - Windows 路径规范化
    winutil         - Windows 工具函数
    elevated_impl   - 提权沙箱实现
    setup_error     - 安装错误处理
```

## 目录结构

```
windows-sandbox-rs/
  src/
    lib.rs                   # 库入口与模块声明（条件编译）
    acl.rs                   # DACL/ACE 管理（添加/撤销访问控制项）
    allow.rs                 # 计算允许读写路径列表
    audit.rs                 # 安全审计（扫描 world-writable 路径）
    cap.rs                   # Capability SID 创建与管理
    conpty/                  # Windows ConPTY（伪终端）
    desktop.rs               # Windows Desktop 隔离
    dpapi.rs                 # DPAPI 数据保护（加密/解密）
    elevated_impl.rs         # 提权模式沙箱实现
    env.rs                   # 环境变量处理（网络阻断、NULL 设备等）
    helper_materialization.rs# 辅助二进制文件部署
    hide_users.rs            # 隐藏沙箱用户配置文件
    identity.rs              # 沙箱凭据管理
    logging.rs               # 沙箱操作日志记录
    path_normalization.rs    # Windows 路径规范化
    policy.rs                # SandboxPolicy 解析（ReadOnly/WorkspaceWrite 等）
    process.rs               # 受限进程创建
    setup_error.rs           # 安装错误报告
    setup_orchestrator.rs    # 沙箱环境初始化编排
    token.rs                 # 受限 Windows 令牌创建
    winutil.rs               # Windows 工具函数
    workspace_acl.rs         # 工作区目录 ACL 保护
    bin/
      setup_main.rs          # codex-windows-sandbox-setup 入口
      command_runner.rs       # codex-command-runner 入口
  build.rs                   # 构建脚本（Windows 资源嵌入）
  Cargo.toml
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `codex-protocol` | 沙箱策略协议类型 |
| `codex-utils-pty` | PTY 工具 |
| `codex-utils-absolute-path` | 绝对路径处理 |
| `codex-utils-string` | 字符串工具 |
| `windows` / `windows-sys` | Windows API 绑定（大量安全与进程管理 API） |
| `tokio` | 异步运行时 |
| `serde` / `serde_json` | 序列化 |
| `chrono` | 时间处理 |
| `tempfile` | 临时文件管理 |
| `dunce` | Windows 路径简化（移除 `\\?\` 前缀） |
| `dirs-next` | 用户目录查找 |
| `rand` | 随机数生成 |
| `base64` | Base64 编解码 |
| `winres` | 构建时 Windows 资源嵌入 |

## 核心接口/API

### 沙箱策略

```rust
pub enum SandboxPolicy {
    ReadOnly { ... },           // 只读模式
    WorkspaceWrite { ... },     // 工作区写入模式
    DangerFullAccess,           // 完全访问（不受沙箱限制）
    ExternalSandbox { ... },    // 外部沙箱
}

pub fn parse_policy(json_or_preset: &str) -> Result<SandboxPolicy>;
```

### 沙箱命令执行

```rust
pub fn run_windows_sandbox_capture(
    policy_json_or_preset: &str,
    sandbox_policy_cwd: &Path,
    codex_home: &Path,
    command: Vec<String>,
    cwd: &Path,
    env_map: HashMap<String, String>,
    timeout_ms: Option<u64>,
    use_private_desktop: bool,
) -> Result<CaptureResult>;

pub struct CaptureResult {
    pub exit_code: i32,
    pub stdout: Vec<u8>,
    pub stderr: Vec<u8>,
    pub timed_out: bool,
}
```

### ACL 管理

```rust
pub fn add_deny_write_ace(path: &Path, sid: *mut c_void) -> Result<bool>;
pub fn ensure_allow_mask_aces(path: &Path, ...) -> Result<()>;
pub fn allow_null_device(sid: *mut c_void);
```

### 令牌创建

```rust
pub fn create_readonly_token_with_cap_from(psid: *mut c_void) -> Result<(HANDLE, ...)>;
pub fn create_workspace_write_token_with_caps_from(base: HANDLE, sids: &[*mut c_void]) -> Result<HANDLE>;
pub fn get_current_token_for_restriction() -> Result<HANDLE>;
```

### 沙箱环境配置

```rust
pub fn run_elevated_setup(request: SandboxSetupRequest) -> Result<()>;
pub fn run_setup_refresh(...) -> Result<()>;
pub fn sandbox_setup_is_complete() -> bool;
pub fn require_logon_sandbox_creds() -> Result<...>;
```

### 二进制工具

- `codex-windows-sandbox-setup`：沙箱环境初始化工具（需管理员权限）
- `codex-command-runner`：在沙箱环境中执行命令的运行器
