# .github -- GitHub 项目配置

## 功能概述

`.github/` 目录包含 Codex 项目在 GitHub 上的全部 CI/CD 工作流配置、自定义 Actions、Issue 模板、PR 模板、Dependabot 配置以及相关辅助脚本。这是项目自动化构建、测试、发布和社区协作的核心配置中心。

## 目录结构

```
.github/
├── workflows/                    # GitHub Actions 工作流定义（详见 workflows/README.md）
├── actions/                      # 自定义复合 Actions（详见 actions/README.md）
│   ├── linux-code-sign/          # Linux 代码签名（cosign）
│   ├── macos-code-sign/          # macOS 代码签名与公证
│   ├── windows-code-sign/        # Windows 代码签名（Azure Trusted Signing）
│   └── setup-bazel-ci/           # Bazel CI 环境准备
├── codex/                        # Codex 相关 Actions 配置
├── scripts/                      # CI 辅助脚本
│   ├── build-zsh-release-artifact.sh   # 构建 zsh 发布产物
│   ├── install-musl-build-tools.sh     # 安装 musl 编译工具
│   ├── run-bazel-ci.sh                 # 运行 Bazel CI
│   └── rusty_v8_bazel.py               # rusty_v8 Bazel 辅助脚本
├── ISSUE_TEMPLATE/               # Issue 模板
│   ├── 1-codex-app.yml           # Codex 应用问题
│   ├── 2-extension.yml           # 扩展问题
│   ├── 3-cli.yml                 # CLI 问题
│   ├── 4-bug-report.yml          # Bug 报告
│   ├── 5-feature-request.yml     # 功能请求
│   └── 6-docs-issue.yml          # 文档问题
├── pull_request_template.md      # PR 模板
├── dependabot.yaml               # Dependabot 自动依赖更新配置
├── blob-size-allowlist.txt       # Blob 大小白名单
├── codex-cli-splash.png          # CLI 启动画面图片
├── dotslash-argument-comment-lint-config.json  # DotSlash lint 配置
├── dotslash-config.json          # DotSlash 配置
└── dotslash-zsh-config.json      # DotSlash zsh 配置
```

## 依赖关系

- **Dependabot 管理的生态系统**：bun、cargo、devcontainers、docker、github-actions、rust-toolchain
- **自定义 Actions**：被 workflows/ 中的工作流引用
- **外部 Actions**：sigstore/cosign-installer（Linux 签名）、Azure Trusted Signing（Windows 签名）、Apple 公证服务（macOS 签名）
