# linux-sandbox

## 功能概述

`codex-linux-sandbox` 是 Codex 在 Linux 平台上的沙箱隔离实现。它综合运用了多层安全机制来限制子进程的权限：

- **Bubblewrap (bwrap)**：文件系统隔离，控制进程对文件系统的可见性和可写性
- **Landlock**：Linux 内核的访问控制框架，作为文件系统限制的备用方案
- **seccomp**：系统调用过滤，用于阻止网络相关系统调用
- **PR_SET_NO_NEW_PRIVS**：防止通过 setuid 等机制提权

该 crate 仅在 Linux 平台可用（通过 `#[cfg(target_os = "linux")]` 条件编译）。在非 Linux 平台调用会 panic。

## 架构说明

```
                    +-------------------+
                    | codex-linux-sandbox|
                    | (linux_run_main)  |
                    +---------+---------+
                              |
              +---------------+---------------+
              |               |               |
              v               v               v
    +---------+----+  +-------+-------+  +----+----------+
    |  Bubblewrap  |  |   Landlock    |  |    seccomp    |
    | (文件系统隔离) |  | (FS 访问控制)  |  | (系统调用过滤) |
    +---------+----+  +-------+-------+  +----+----------+
              |               |               |
              v               v               v
    +---------+----+  +-------+-------+  +----+----------+
    |   launcher   |  | landlock crate|  | seccompiler   |
    | (bwrap 启动器)|  |              |  |   crate       |
    +--------------+  +---------------+  +---------------+

依赖上游 crate:
    codex-core          (核心配置与错误类型)
    codex-protocol      (沙箱策略协议定义)
    codex-sandboxing    (沙箱通用工具，如查找系统 bwrap)
```

## 目录结构

```
linux-sandbox/
  src/
    lib.rs                  # 库入口，仅导出 run_main()
    main.rs                 # 二进制入口
    linux_run_main.rs       # Linux 主函数逻辑
    linux_run_main_tests.rs # 主函数测试
    bwrap.rs                # Bubblewrap 参数构建与调用
    landlock.rs             # Landlock + seccomp 沙箱实现
    launcher.rs             # Bubblewrap 启动器（系统版本 vs 内嵌版本）
    proxy_routing.rs        # 代理路由支持
    vendored_bwrap.rs       # 内嵌的 Bubblewrap 实现
  build.rs                  # 构建脚本（编译 C 依赖）
  Cargo.toml
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `codex-core` | Codex 核心库，提供错误类型和配置 |
| `codex-protocol` | 沙箱策略协议定义（SandboxPolicy 等） |
| `codex-sandboxing` | 沙箱通用工具（查找系统 bwrap） |
| `codex-utils-absolute-path` | 绝对路径处理 |
| `landlock` | Linux Landlock 访问控制 API |
| `seccompiler` | seccomp BPF 过滤器生成与应用 |
| `libc` | 底层系统调用（prctl, execv 等） |
| `clap` | CLI 参数解析 |
| `serde` / `serde_json` | 配置序列化 |
| `url` | URL 解析（代理路由） |
| `cc` / `pkg-config` | 构建依赖（编译 C 代码） |

## 核心接口/API

### `run_main()`

```rust
pub fn run_main() -> !;
```

沙箱的主入口函数。在 Linux 上执行完整的沙箱初始化流程；在非 Linux 平台上会 panic。

### `apply_sandbox_policy_to_current_thread()`

（内部函数）将沙箱策略应用到当前线程：

```rust
pub(crate) fn apply_sandbox_policy_to_current_thread(
    sandbox_policy: &SandboxPolicy,
    network_sandbox_policy: NetworkSandboxPolicy,
    cwd: &Path,
    apply_landlock_fs: bool,
    allow_network_for_proxy: bool,
    proxy_routed_network: bool,
) -> Result<()>;
```

该函数负责：
1. 在需要时启用 `PR_SET_NO_NEW_PRIVS`
2. 安装网络 seccomp 过滤器（阻止 `connect`、`bind`、`socket` 等系统调用）
3. 可选地安装 Landlock 文件系统规则

### seccomp 网络过滤

两种模式：
- **Restricted**：阻止所有网络系统调用，仅允许 AF_UNIX 套接字
- **ProxyRouted**：仅允许 AF_INET/AF_INET6（用于代理桥接），阻止 AF_UNIX

### Bubblewrap 启动器

```rust
pub(crate) fn exec_bwrap(argv: Vec<String>, preserved_files: Vec<File>) -> !;
```

自动选择系统安装的 bwrap 或内嵌版本来执行文件系统隔离。
