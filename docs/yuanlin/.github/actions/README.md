# .github/actions -- 自定义 GitHub Actions

## 功能概述

本目录包含 Codex 项目的自定义复合 Actions（Composite Actions），主要用于发布流程中的代码签名和 CI 环境准备。这些 Actions 被 `.github/workflows/` 中的发布与 CI 工作流引用。

## 目录结构

```
actions/
├── linux-code-sign/
│   └── action.yml            # Linux 制品签名（使用 sigstore/cosign）
├── macos-code-sign/
│   ├── action.yml            # macOS 代码签名、公证与清理
│   ├── codex.entitlements.plist  # macOS 权限声明文件
│   └── notary_helpers.sh     # 公证辅助脚本
├── windows-code-sign/
│   └── action.yml            # Windows 二进制签名（Azure Trusted Signing）
└── setup-bazel-ci/
    └── action.yml            # Bazel CI 运行器准备（缓存、测试前置依赖）
```

## 各 Action 说明

### linux-code-sign
- **用途**：使用 cosign 对 Linux 构建产物进行签名
- **输入参数**：`target`（目标三元组）、`artifacts-dir`（产物目录路径）

### macos-code-sign
- **用途**：配置 Apple 证书，对 macOS 二进制文件和 DMG 进行代码签名并提交公证
- **输入参数**：`target`（目标三元组）、`sign-binaries`、`sign-dmg`、Apple 证书相关 secrets
- **辅助文件**：`codex.entitlements.plist`（权限声明）、`notary_helpers.sh`（公证工具函数）

### windows-code-sign
- **用途**：通过 Azure Trusted Signing 对 Windows 可执行文件进行签名
- **输入参数**：`target`（目标三元组）、Azure 相关凭据（client-id、tenant-id、subscription-id）

### setup-bazel-ci
- **用途**：为 Bazel CI 作业准备运行环境，包括共享缓存恢复和可选的测试前置工具安装
- **输入参数**：`target`（用于缓存命名空间的目标三元组）、`install-test-prereqs`（是否安装 Node.js 和 DotSlash）
- **输出**：`cache-hit`（是否精确命中缓存）

## 依赖关系

- **被引用方**：`rust-release.yml`、`rust-release-windows.yml`、`bazel.yml` 等工作流
- **外部依赖**：sigstore/cosign-installer、Azure Trusted Signing 服务、Apple 公证服务
