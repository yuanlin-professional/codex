# `lib.rs` — 进程安全加固

## 功能概述

在 `main()` 之前执行进程安全加固措施。禁用核心转储（RLIMIT_CORE=0）、防止调试器附加（Linux PR_SET_DUMPABLE=0、macOS PT_DENY_ATTACH）、清除危险环境变量（Linux 的 LD_* 和 macOS 的 DYLD_*）。设计为通过 `#[ctor::ctor]` 在程序启动前调用。

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `pre_main_hardening` | `()` | 分发到平台特定的加固函数 |
| `pre_main_hardening_linux` | `()` | Linux: prctl + rlimit + 清除 LD_ 变量 |
| `pre_main_hardening_macos` | `()` | macOS: ptrace deny + rlimit + 清除 DYLD_ 变量 |
| `pre_main_hardening_bsd` | `()` | BSD: rlimit + 清除 LD_ 变量 |
| `set_core_file_size_limit_to_zero` | `()` | 设置 RLIMIT_CORE 为 0 |
| `env_keys_with_prefix` | `(vars, prefix) -> Vec<OsString>` | 过滤匹配前缀的环境变量键 |

## 依赖关系

- **引用的 crate**: `libc`
- **被引用方**: Codex CLI 二进制入口（通过 ctor 属性）

## 实现备注

- 加固失败时以特定退出码退出（5=prctl、6=ptrace、7=rlimit）。
- env_keys_with_prefix 正确处理非 UTF-8 环境变量键。
