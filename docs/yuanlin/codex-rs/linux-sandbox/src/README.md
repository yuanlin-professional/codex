# linux-sandbox/src — 源代码目录

## 功能概述
linux-sandbox crate 的源代码实现目录。Linux 平台沙箱实现，使用 bubblewrap 和 Landlock。

## 目录结构
| 文件 | 说明 |
|------|------|
| `bwrap.rs` | bubblewrap 封装 |
| `landlock.rs` | Landlock LSM 支持 |
| `launcher.rs` | 沙箱启动器 |
| `lib.rs` | 库入口 |
| `linux_run_main.rs` | Linux 主运行逻辑 |
| `linux_run_main_tests.rs` | 主运行逻辑测试 |
| `main.rs` | 程序入口 |
| `proxy_routing.rs` | 代理路由 |
| `vendored_bwrap.rs` | 内置 bwrap |
