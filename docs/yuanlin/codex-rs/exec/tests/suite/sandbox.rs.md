# `sandbox.rs` — 测试模块

## 测试覆盖范围

测试沙箱策略的实际执行效果，验证 macOS (Seatbelt) 和 Linux (Landlock) 沙箱对文件系统写入、Unix socket 通信、Python 多进程锁等操作的控制。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `python_multiprocessing_lock_works_under_sandbox` | Python multiprocessing Lock 在沙箱下正常工作 |
| `python_getpwuid_works_under_sandbox` | Python getpwuid 调用在只读沙箱下正常工作 |
| `sandbox_distinguishes_command_and_policy_cwds` | 沙箱正确区分命令 cwd 和策略 cwd 的写入权限 |
| `sandbox_blocks_first_time_dot_codex_creation` | 沙箱阻止在工作目录下创建 .codex 目录 |
| `allow_unix_socketpair_recvfrom` | Unix socketpair + recvfrom 在沙箱下被允许 |
