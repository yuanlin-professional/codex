# .github/workflows -- CI/CD 工作流

## 功能概述

本目录定义了 Codex 项目全部的 GitHub Actions 工作流。工作流按照"PR 快速反馈 + main 分支完整验证"的策略进行拆分，确保 PR 阶段获得快速信号，同时 main 分支获得完整的跨平台验证。

## 目录结构

```
workflows/
├── README.md                              # 工作流策略说明
├── bazel.yml                              # Bazel 构建与测试（PR + main）
├── rust-ci.yml                            # Rust PR 轻量检查（fmt、shear、lint）
├── rust-ci-full.yml                       # Rust main 分支完整验证（clippy、nextest、release build）
├── rust-release.yml                       # Rust 发布工作流（tag 触发）
├── rust-release-prepare.yml               # 发布准备（定时 + 手动触发）
├── rust-release-windows.yml               # Windows 平台发布（被调用子工作流）
├── rust-release-zsh.yml                   # zsh 补全发布（被调用子工作流）
├── rust-release-argument-comment-lint.yml # argument-comment-lint 发布
├── rusty-v8-release.yml                   # rusty_v8 musl 构建发布
├── sdk.yml                                # SDK 构建与测试
├── ci.yml                                 # 通用 CI（PR + main）
├── cargo-deny.yml                         # Cargo 依赖审计（deny）
├── blob-size-policy.yml                   # Blob 大小策略检查
├── cla.yml                                # CLA 助手
├── codespell.yml                          # 拼写检查
├── close-stale-contributor-prs.yml        # 关闭过期的贡献者 PR
├── issue-deduplicator.yml                 # Issue 去重
├── issue-labeler.yml                      # Issue 自动标签
├── v8-canary.yml                          # V8 canary 构建测试
├── Dockerfile.bazel                       # Bazel CI 用 Docker 镜像
└── zstd/                                  # zstd 压缩相关资源
```

## 工作流分类说明

### PR 阶段（快速反馈）
| 工作流 | 说明 |
|--------|------|
| `bazel.yml` | 主要的 PR 前合并验证路径，运行 Bazel test 和 Bazel clippy |
| `rust-ci.yml` | 轻量 Cargo 检查：`cargo fmt --check`、`cargo shear`、跨平台 argument-comment-lint |
| `ci.yml` | 通用 CI 检查 |
| `cargo-deny.yml` | Cargo 依赖许可证与安全审计 |
| `blob-size-policy.yml` | 防止意外提交大文件 |
| `codespell.yml` | 拼写检查 |
| `v8-canary.yml` | V8 相关路径变更时的 canary 构建 |

### main 分支（完整验证）
| 工作流 | 说明 |
|--------|------|
| `bazel.yml` | 合并后重新验证 Bazel 路径，保持 BuildBuddy 缓存热度 |
| `rust-ci-full.yml` | 完整 Cargo 验证：clippy 矩阵、nextest 矩阵、release-profile 构建、跨平台 lint、Linux remote-env 测试 |
| `sdk.yml` | SDK 构建与测试 |

### 发布工作流
| 工作流 | 说明 |
|--------|------|
| `rust-release.yml` | Rust 主发布流程（git tag 触发） |
| `rust-release-prepare.yml` | 发布准备，定时每4小时或手动触发 |
| `rust-release-windows.yml` | Windows 平台构建与签名子工作流 |
| `rust-release-zsh.yml` | zsh 补全构建子工作流 |
| `rust-release-argument-comment-lint.yml` | argument-comment-lint 工具发布 |
| `rusty-v8-release.yml` | rusty_v8 musl 静态库构建与发布 |

### 社区管理
| 工作流 | 说明 |
|--------|------|
| `cla.yml` | 贡献者许可协议助手 |
| `close-stale-contributor-prs.yml` | 自动关闭过期贡献者 PR |
| `issue-deduplicator.yml` | Issue 自动去重检测 |
| `issue-labeler.yml` | Issue 自动标签分类 |

## 依赖关系

- **自定义 Actions**：引用 `.github/actions/` 下的签名与 Bazel 设置 actions
- **外部 Actions**：actions/checkout、dtolnay/rust-toolchain、taiki-e/install-action 等
- **CI 脚本**：引用 `.github/scripts/` 下的辅助脚本
