# sandboxing/src — 源代码目录

## 功能概述
sandboxing crate 的源代码实现目录。跨平台沙箱管理器，支持 bubblewrap、Landlock 和 Seatbelt。

## 目录结构
| 文件 | 说明 |
|------|------|
| `bwrap.rs` | bubblewrap 沙箱 |
| `bwrap_tests.rs` | bwrap 测试 |
| `landlock.rs` | Landlock 沙箱 |
| `landlock_tests.rs` | Landlock 测试 |
| `lib.rs` | 库入口 |
| `manager.rs` | 沙箱管理器 |
| `manager_tests.rs` | 管理器测试 |
| `policy_transforms.rs` | 策略转换 |
| `policy_transforms_tests.rs` | 策略转换测试 |
| `seatbelt.rs` | macOS Seatbelt 沙箱 |
| `seatbelt_tests.rs` | Seatbelt 测试 |
