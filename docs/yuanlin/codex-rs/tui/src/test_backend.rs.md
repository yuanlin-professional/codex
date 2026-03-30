# `test_backend.rs` — VT100 模拟终端后端

## 功能概述

封装 `CrosstermBackend` 和 `vt100::Parser` 以模拟真实终端，用于快照测试。
避免调用 crossterm 直接写入 stdout 的方法（如获取终端大小、光标位置），
而是从 vt100 虚拟屏幕读取这些信息。

## 关键结构体/枚举

| 名称 | 类型 | 说明 |
|------|------|------|
| `VT100Backend` | struct | 基于 vt100 解析器的 ratatui Backend 实现 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `new` | `(width, height) -> Self` | 创建指定尺寸的虚拟终端 |
| `vt100` | `(&self) -> &vt100::Parser` | 获取底层解析器引用 |

## 依赖关系

- **引用的 crate**: `ratatui`, `crossterm`, `vt100`

## 实现备注

- 构造时强制启用颜色输出（`force_color_output(true)`）以在测试中正确捕获样式。
- `Display` 实现输出 vt100 screen 内容，用于 insta 快照断言。
