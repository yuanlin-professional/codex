# sleep-inhibitor/src — 源代码目录

## 功能概述
sleep-inhibitor 工具 crate 的源代码实现目录。阻止系统进入休眠状态。

## 目录结构
| 文件 | 说明 |
|------|------|
| `dummy.rs` | 虚拟实现（无操作） |
| `iokit_bindings.rs` | macOS IOKit 绑定 |
| `lib.rs` | 库入口 |
| `linux_inhibitor.rs` | Linux 休眠抑制 |
| `macos.rs` | macOS 休眠抑制 |
| `windows_inhibitor.rs` | Windows 休眠抑制 |
