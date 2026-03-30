# v8-poc

## 功能概述

`codex-v8-poc` 是 Codex 项目中 V8 JavaScript 引擎集成的概念验证（Proof of Concept）模块。它验证了通过 Bazel 构建系统将 V8 引擎嵌入 Rust 项目的可行性，并提供了基础的 V8 初始化和 JavaScript 表达式求值能力。该模块为 `codex-code-mode` 等正式使用 V8 的模块奠定了构建和集成基础。

## 架构说明

该模块极为精简，仅包含两个工具函数和一套验证测试：

### 功能组件

1. **`bazel_target()`** - 返回该 crate 的 Bazel 构建标签，用于构建系统集成验证。
2. **`embedded_v8_version()`** - 返回当前嵌入的 V8 引擎版本号。

### 测试验证

测试套件验证了完整的 V8 集成链路：
- V8 平台初始化（`v8::V8::initialize_platform` + `v8::V8::initialize`）
- Isolate 创建和作用域管理
- Context 创建
- JavaScript 源码编译和执行
- 整数运算（`1 + 2`）和字符串拼接（`'hello ' + 'world'`）

使用 `std::sync::Once` 确保 V8 仅初始化一次（全局唯一约束）。

### 作为 POC 的定位

该模块不包含生产逻辑，其存在意义在于：
- 验证 V8 Rust 绑定在项目构建环境中的兼容性
- 为 Bazel 构建配置提供参考
- 作为 V8 集成的最小可运行示例

## 目录结构

```
v8-poc/
  Cargo.toml
  src/
    lib.rs   # 工具函数和集成验证测试
```

## 依赖关系

| 依赖 | 用途 |
|------|------|
| `v8` | V8 JavaScript 引擎 Rust 绑定 |

该模块仅依赖 `v8` crate，是项目中依赖最精简的模块之一。

## 核心接口/API

### 函数

```rust
/// 返回该 crate 的 Bazel 构建标签
/// 返回值: "//codex-rs/v8-poc:v8-poc"
#[must_use]
pub fn bazel_target() -> &'static str;

/// 返回嵌入的 V8 引擎版本号
#[must_use]
pub fn embedded_v8_version() -> &'static str;
```

### 使用场景

该模块主要供 Codex 构建系统和 CI 使用，验证 V8 集成的正确性。生产代码中的 V8 使用应参考 `codex-code-mode` 模块。
