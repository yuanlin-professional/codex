# codex-rs/scripts -- Rust 工作空间辅助脚本

## 功能概述

`codex-rs/scripts/` 目录包含 Codex Rust 工作空间的平台特定辅助脚本，目前主要提供 Windows 开发环境的自动化配置。

## 目录结构

```
codex-rs/scripts/
└── setup-windows.ps1    # Windows 开发环境配置脚本（PowerShell）
```

## 脚本说明

### setup-windows.ps1

Windows 平台的开发环境一键配置脚本（约 246 行），用于在全新的 Windows 机器上准备 codex-rs 的完整构建环境。

**功能清单**：
- 通过 winget 安装 Rust 工具链（rustup）及所需组件
- 安装 Visual Studio 2022 Build Tools（MSVC + Windows SDK）
- 安装辅助 CLI 工具：git、ripgrep (rg)、just、cmake
- 通过 cargo 安装 cargo-insta（快照测试工具）
- 确保当前会话的 PATH 包含 Cargo bin 目录
- 执行 `cargo build` 构建工作空间

**使用方式**：
```powershell
# 以管理员权限运行 PowerShell
powershell -ExecutionPolicy Bypass -File scripts/setup-windows.ps1

# 跳过最终构建步骤
powershell -ExecutionPolicy Bypass -File scripts/setup-windows.ps1 -SkipBuild
```

**前置条件**：
- 需要 winget（Windows 包管理器，大多数 Windows 10/11 已预装）
- 安装 VS Build Tools 需要管理员权限
- 脚本可重复运行，winget/cargo 会自动跳过已安装项

## 依赖关系

- **外部依赖**：winget、Visual Studio Build Tools、rustup
- **被调用方**：新 Windows 开发者环境配置、CI Windows 构建环境准备
