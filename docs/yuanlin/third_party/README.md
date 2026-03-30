# third_party -- 第三方依赖资源

## 功能概述

`third_party/` 目录存放 Codex 项目引用的第三方库的许可证文件、Bazel 构建规则和版本配置。这些资源主要服务于 Bazel 构建系统中的第三方依赖集成。

## 目录结构

```
third_party/
├── meriyah/
│   └── LICENSE                  # meriyah（JavaScript 解析器）许可证
├── v8/
│   ├── README.md                # rusty_v8 消费者构建说明
│   ├── BUILD.bazel              # V8 Bazel 构建规则
│   └── v8_crate.BUILD.bazel     # V8 crate 的 Bazel 构建定义
└── wezterm/
    └── LICENSE                  # wezterm（终端模拟器）许可证
```

## 各组件说明

### meriyah
- **类型**：JavaScript 解析器库
- **用途**：用于 Codex 中的 JavaScript 代码解析
- **内容**：仅包含许可证文件

### v8 / rusty_v8
- **类型**：V8 JavaScript 引擎的 Rust 绑定
- **用途**：为 Codex 提供 JavaScript 执行能力
- **固定版本**：Rust crate v8 = 146.4.0，V8 源码 14.6.202.9
- **Bazel 集成**：
  - Windows 使用上游 `denoland/rusty_v8` 发布归档
  - Darwin/GNU Linux/musl Linux 使用源码构建归档
  - 发布的 musl 静态库由 `rusty-v8-release.yml` 工作流构建
- **Cargo 集成**：默认使用预构建归档，Bazel 通过 `RUSTY_V8_ARCHIVE` 和 `RUSTY_V8_SRC_BINDING_PATH` 环境变量覆盖
- **注意**：归档和绑定文件必须与 `codex-rs/Cargo.lock` 中解析的 v8 crate 版本精确匹配

### wezterm
- **类型**：终端模拟器库
- **用途**：Codex TUI 中使用的终端相关功能
- **内容**：仅包含许可证文件

## 依赖关系

- **被引用方**：`MODULE.bazel`、Bazel 构建规则、Cargo 构建系统
- **发布工作流**：`rusty-v8-release.yml`（构建和发布 musl 静态库）
- **补丁**：`patches/` 目录中包含多个 V8 相关补丁
