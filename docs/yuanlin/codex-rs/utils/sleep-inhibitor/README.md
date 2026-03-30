# sleep-inhibitor

## 功能概述

`codex-utils-sleep-inhibitor` 提供了跨平台的系统休眠抑制功能。在 AI 模型执行任务（turn）期间防止系统进入空闲休眠状态，确保长时间运行的操作不会被系统休眠中断。

平台实现：
- **macOS** -- 使用 IOKit 电源断言（`IOPMAssertionCreateWithName`）
- **Linux** -- 使用 `systemd-inhibit` 或 `gnome-session-inhibit`
- **Windows** -- 使用 `PowerCreateRequest` + `PowerSetRequest`（`PowerRequestSystemRequired`）
- **其他平台** -- 无操作（dummy）后端

## 目录结构

```
sleep-inhibitor/
├── Cargo.toml
└── src/
    ├── lib.rs                # SleepInhibitor 主类型
    ├── macos.rs              # macOS 实现
    ├── linux_inhibitor.rs    # Linux 实现
    ├── windows_inhibitor.rs  # Windows 实现
    └── dummy.rs              # 其他平台的无操作实现
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `tracing` | 日志记录 |
| `core-foundation`（macOS） | macOS CoreFoundation 绑定 |
| `libc`（Linux） | Linux 系统调用 |
| `windows-sys`（Windows） | Windows API 绑定 |

## 核心接口/API

### `SleepInhibitor`

```rust
// 创建抑制器（可启用/禁用）
let mut inhibitor = SleepInhibitor::new(true);  // enabled = true

// 设置 turn 运行状态（自动管理休眠抑制）
inhibitor.set_turn_running(true);   // 开始 turn -> 抑制休眠
inhibitor.set_turn_running(false);  // 结束 turn -> 允许休眠

// 查询当前 turn 状态
let running = inhibitor.is_turn_running();
```

行为说明：
- `enabled = false` 时，所有操作为无操作
- 多次调用 `set_turn_running(true)` 是幂等的
- 可以安全地在启用/禁用之间多次切换
