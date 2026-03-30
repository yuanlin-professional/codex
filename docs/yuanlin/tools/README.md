# tools -- 开发工具

## 功能概述

`tools/` 目录包含 Codex 项目的独立开发工具，目前主要包含 `argument-comment-lint`，一个基于 Dylint 的 Rust 代码 lint 工具，用于强制执行函数调用参数注释的规范格式。

## 目录结构

```
tools/
└── argument-comment-lint/
    ├── README.md                     # 详细使用说明
    ├── Cargo.toml                    # Rust crate 配置
    ├── Cargo.lock                    # 依赖锁文件
    ├── BUILD.bazel                   # Bazel 构建定义
    ├── lint_aspect.bzl               # Bazel lint aspect 定义
    ├── rust-toolchain                # 固定的 Rust nightly 工具链版本
    ├── driver.rs                     # rustc_driver 入口
    ├── src/                          # Lint 规则实现源码
    ├── argument-comment-lint         # DotSlash 可执行文件（预构建入口）
    ├── run.py                        # 源码构建路径运行脚本
    ├── run-prebuilt-linter.py        # 预构建路径运行脚本
    ├── wrapper_common.py             # 运行器公共逻辑
    ├── test_wrapper_common.py        # 公共逻辑测试
    └── ui/                           # UI 相关资源
```

## 工具说明

### argument-comment-lint

一个独立的 Dylint lint 库，强制要求 Rust 代码中的函数调用参数注释使用 `/*param*/` 格式。

提供两个 lint 规则：

1. **`argument_comment_mismatch`**（默认 warn）：验证已有的 `/*param*/` 注释与被调用函数的参数名匹配
2. **`uncommented_anonymous_literal_argument`**（默认 allow，仓库级别提升为 error）：标记没有注释的匿名字面量参数（`None`、`true`、`false`、数字字面量）

#### 运行方式
```bash
# Bazel 全仓库运行（推荐）
just argument-comment-lint
bazel build --config=argument-comment-lint -- //codex-rs/...

# 预构建路径运行（针对特定包）
just argument-comment-lint -p codex-core

# 源码构建运行（开发 lint 规则时使用）
./tools/argument-comment-lint/run.py -p codex-core
```

#### 发布产物
GitHub Releases 发布预构建的 DotSlash 文件，支持 macOS arm64、Linux arm64、Linux x64 和 Windows x64 平台。

## 依赖关系

- **工具链**：固定的 Rust nightly 版本（nightly-2025-09-18），需要 llvm-tools-preview、rustc-dev、rust-src 组件
- **外部依赖**：cargo-dylint、dylint-link
- **被调用方**：`rust-ci.yml`、`bazel.yml`、`justfile` 任务
- **发布工作流**：`rust-release-argument-comment-lint.yml`
