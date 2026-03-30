# process-hardening

## 功能概述

`codex-process-hardening` 提供进程级别的安全加固功能，旨在在程序启动的最早阶段（`pre-main`）执行一系列安全措施，防止进程被调试、注入或产生核心转储等安全风险。

该 crate 支持多个平台（Linux、macOS、FreeBSD、OpenBSD、Windows），针对每个平台实施不同的加固策略。它通常与 `#[ctor::ctor]` 配合使用，在 `main()` 函数之前执行。

## 架构说明

```
              +-------------------------+
              |  pre_main_hardening()   |  (统一入口)
              +------------+------------+
                           |
         +-----------------+------------------+-------------------+
         |                 |                  |                   |
         v                 v                  v                   v
+--------+------+  +-------+-------+  +------+--------+  +------+--------+
| Linux/Android |  |     macOS     |  | FreeBSD/OpenBSD|  |   Windows    |
| - PR_SET_     |  | - PT_DENY_    |  | - RLIMIT_CORE |  | (TODO)       |
|   DUMPABLE=0  |  |   ATTACH      |  |   = 0         |  |              |
| - RLIMIT_CORE |  | - RLIMIT_CORE |  | - 清除 LD_*   |  |              |
|   = 0         |  |   = 0         |  |   环境变量     |  |              |
| - 清除 LD_*   |  | - 清除 DYLD_* |  |               |  |              |
|   环境变量     |  |   环境变量     |  |               |  |              |
+---------------+  +---------------+  +----------------+  +--------------+
```

## 目录结构

```
process-hardening/
  src/
    lib.rs    # 全部实现集中在此文件中
  Cargo.toml
```

该 crate 结构非常精简，所有逻辑都在 `lib.rs` 单文件中通过条件编译实现。

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `libc` | 底层系统调用（prctl、ptrace、setrlimit 等） |

开发依赖：
| 依赖 | 用途 |
|------|------|
| `pretty_assertions` | 测试断言美化输出 |

## 核心接口/API

### `pre_main_hardening()`

```rust
pub fn pre_main_hardening();
```

统一入口函数，根据目标平台自动选择对应的加固策略。该函数应在 `main()` 之前调用。

#### Linux/Android 加固措施

1. **禁用 ptrace attach**：调用 `prctl(PR_SET_DUMPABLE, 0)` 将进程标记为不可转储，阻止其他进程通过 ptrace 附加
2. **禁用核心转储**：调用 `setrlimit(RLIMIT_CORE, 0)` 将核心文件大小限制设为 0
3. **清除危险环境变量**：移除所有以 `LD_` 开头的环境变量（如 `LD_PRELOAD`），防止动态链接器注入

#### macOS 加固措施

1. **禁止调试器附加**：调用 `ptrace(PT_DENY_ATTACH)` 阻止调试器附加到进程
2. **禁用核心转储**：设置 `RLIMIT_CORE` 为 0
3. **清除危险环境变量**：移除所有以 `DYLD_` 开头的环境变量，防止 dyld 库加载劫持

#### FreeBSD/OpenBSD 加固措施

1. **禁用核心转储**：设置 `RLIMIT_CORE` 为 0
2. **清除 LD_* 环境变量**

#### Windows 加固措施

当前为占位实现（TODO），尚未实现具体加固逻辑。

### 内部辅助函数

```rust
// 将核心文件大小限制设为 0
fn set_core_file_size_limit_to_zero();

// 查找所有以指定前缀开头的环境变量键名
fn env_keys_with_prefix<I>(vars: I, prefix: &[u8]) -> Vec<OsString>;
```

### 退出码

| 退出码 | 含义 |
|--------|------|
| 5 | `prctl(PR_SET_DUMPABLE)` 失败（Linux） |
| 6 | `ptrace(PT_DENY_ATTACH)` 失败（macOS） |
| 7 | `setrlimit(RLIMIT_CORE)` 失败（Unix 通用） |
