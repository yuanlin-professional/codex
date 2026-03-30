# argument-comment-lint - 参数注释检查工具

## 功能概述

自定义的 Rust lint 工具，用于检查函数调用中的参数注释是否与参数名称匹配。这是一个 Clippy 扩展，强制要求在调用含有字面量参数的函数时添加注释说明。

## 目录结构

| 文件/目录 | 说明 |
|-----------|------|
| `src/` | lint 规则源码 |
| `ui/` | UI 测试用例（测试 lint 输出） |
| `.cargo/` | Cargo 构建配置 |
| `Cargo.toml` | Rust 项目配置 |
| `driver.rs` | Clippy 驱动程序入口 |
| `run.py` | 运行脚本 |
| `run-prebuilt-linter.py` | 使用预编译 linter 运行 |
| `lint_aspect.bzl` | Bazel lint 切面定义 |
| `README.md` | 使用说明 |
