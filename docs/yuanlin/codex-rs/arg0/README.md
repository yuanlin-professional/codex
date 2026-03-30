# arg0

## 功能概述

`codex-arg0` 是 Codex 项目的进程名称分发（argv[0] dispatch）模块，利用 Unix 系统中 `argv[0]`（进程名称）可以与实际可执行文件路径不同的特性，实现单一二进制文件模拟多个 CLI 工具的功能。Codex 通过创建指向自身的符号链接（如 `apply_patch`、`codex-linux-sandbox`、`codex-execve-wrapper`），在启动时根据 `argv[0]` 分发到不同的执行路径，避免了需要部署多个独立可执行文件的复杂性。

## 架构说明

### arg0 分发流程

```
启动 -> 检查 argv[0]
         |
         |- "codex-execve-wrapper" -> codex_shell_escalation::run_shell_escalation_execve_wrapper
         |- "codex-linux-sandbox"  -> codex_linux_sandbox::run_main (永不返回)
         |- "apply_patch"         -> codex_apply_patch::main
         |- "--codex-run-as-apply-patch" (argv[1]) -> 内联 apply_patch 执行
         |
         |- 其他 -> load_dotenv() + prepend_path_entry_for_codex_aliases()
                     -> 创建 Tokio 运行时 -> 执行 main_fn
```

### PATH 注入机制

`prepend_path_entry_for_codex_aliases()` 在 `~/.codex/tmp/arg0/` 下创建临时目录，在其中建立指向当前可执行文件的符号链接（Unix）或批处理脚本（Windows），然后将该目录添加到 PATH 前端。这使得子进程可以通过 `apply_patch` 等命令名直接调用 Codex 的内置功能。

### 临时目录管理

- 每次启动创建新的临时目录（`codex-arg0*`）
- 使用文件锁（`.lock`）防止并发清理
- Janitor 清理机制自动清除无锁的过期目录
- `Arg0PathEntryGuard` RAII 守卫确保进程退出时释放资源

### .env 文件加载

在创建任何线程之前，从 `~/.codex/.env` 加载环境变量。安全机制：拒绝以 `CODEX_` 为前缀的变量，防止 `.env` 文件覆盖 Codex 内部配置。

## 目录结构

```
arg0/
  Cargo.toml
  src/
    lib.rs   # 完整实现：arg0 分发、PATH 注入、.env 加载、临时目录管理
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `codex-apply-patch` | apply_patch 功能分发 |
| `codex-linux-sandbox` | Linux 沙箱分发 |
| `codex-sandboxing` | 沙箱常量（CODEX_LINUX_SANDBOX_ARG0） |
| `codex-shell-escalation` | execve wrapper 分发 |
| `codex-utils-home-dir` | Codex 主目录定位 |
| `dotenvy` | .env 文件解析 |
| `tempfile` | 临时目录管理 |
| `tokio` | 多线程运行时（16 MiB 栈） |
| `anyhow` | 错误处理 |

## 核心接口/API

### 主要函数

```rust
/// 检查 argv[0] 并分发到对应的子命令，若为普通调用则返回 PATH 守卫
pub fn arg0_dispatch() -> Option<Arg0PathEntryGuard>;

/// 执行 arg0 分发，若为普通调用则创建 Tokio 运行时并执行 main_fn
pub fn arg0_dispatch_or_else<F, Fut>(main_fn: F) -> anyhow::Result<()>
where
    F: FnOnce(Arg0DispatchPaths) -> Fut,
    Fut: Future<Output = anyhow::Result<()>>;

/// 创建临时目录并注入 PATH
pub fn prepend_path_entry_for_codex_aliases() -> std::io::Result<Arg0PathEntryGuard>;
```

### 类型定义

```rust
pub struct Arg0DispatchPaths {
    pub codex_self_exe: Option<PathBuf>,            // 当前 Codex 可执行文件路径
    pub codex_linux_sandbox_exe: Option<PathBuf>,   // Linux 沙箱别名路径
    pub main_execve_wrapper_exe: Option<PathBuf>,   // execve wrapper 路径
}

pub struct Arg0PathEntryGuard {
    // RAII 守卫，维护临时目录和文件锁
}
```
