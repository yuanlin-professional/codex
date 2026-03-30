# elapsed

## 功能概述

`codex-utils-elapsed` 提供了人类可读的时间间隔格式化函数。将 `Duration` 或 `Instant` 转换为紧凑的字符串表示，适用于 CLI 输出和日志记录。

格式化规则：
- 小于 1 秒: `{毫秒}ms`（例如 `250ms`）
- 1 秒到 60 秒: `{秒:.2}s`（例如 `1.50s`）
- 60 秒及以上: `{分}m {秒:02}s`（例如 `1m 15s`）

## 目录结构

```
elapsed/
├── Cargo.toml
└── src/
    └── lib.rs          # format_elapsed() 和 format_duration() 函数
```

## 依赖关系

无外部依赖，仅使用标准库。

## 核心接口/API

### `format_elapsed(start_time: Instant) -> String`

计算从 `start_time` 到现在的经过时间，并格式化为人类可读字符串。

### `format_duration(duration: Duration) -> String`

将 `Duration` 格式化为人类可读字符串。

```rust
use std::time::Duration;

assert_eq!(format_duration(Duration::from_millis(250)), "250ms");
assert_eq!(format_duration(Duration::from_millis(1_500)), "1.50s");
assert_eq!(format_duration(Duration::from_millis(75_000)), "1m 15s");
```
