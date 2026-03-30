# `_runtime_setup.py` -- Codex CLI 运行时自动下载与安装管理

## 功能概述

本模块负责管理 Codex CLI 二进制运行时（`codex-cli-bin`）的版本锁定、下载与安装。它通过 GitHub Releases API 下载对应平台的预编译二进制文件，自动解压缩后通过 pip 安装到目标 Python 环境中。模块支持 macOS（arm64/x86_64）、Linux（aarch64/x86_64）和 Windows（aarch64/x86_64）三大平台，并提供多种下载策略（直接 URL、API + Token、gh CLI）作为回退机制。

## 关键类/函数

| 名称 | 类型 | 说明 |
|------|------|------|
| `PINNED_RUNTIME_VERSION` | 常量 | 当前锁定的运行时版本号 |
| `RuntimeSetupError` | 异常类 | 运行时安装过程中的错误基类 |
| `pinned_runtime_version()` | 函数 | 返回当前锁定的运行时版本字符串 |
| `ensure_runtime_package_installed()` | 函数 | 核心入口：确保指定 Python 环境中已安装正确版本的运行时包 |
| `platform_asset_name()` | 函数 | 根据当前平台和架构返回对应的发布资产文件名 |
| `runtime_binary_name()` | 函数 | 返回运行时二进制文件名（Windows 为 `codex.exe`，其他为 `codex`） |
| `_download_release_archive()` | 内部函数 | 从 GitHub Releases 下载发布包，支持直接 URL、API Token 和 gh CLI 三种策略 |
| `_extract_runtime_binary()` | 内部函数 | 从 tar.gz 或 zip 归档中提取 codex 二进制文件 |
| `_stage_runtime_package()` | 内部函数 | 调用 update_sdk_artifacts 脚本构建可安装的运行时 Python 包 |
| `_install_runtime_package()` | 内部函数 | 通过 pip 将构建好的运行时包安装到目标环境 |
| `_normalized_package_version()` | 内部函数 | 将版本字符串中的 `-alpha.`/`-beta.` 规范化为 PEP 440 兼容格式 |

## 依赖关系

- **引用的模块**: `importlib`, `json`, `os`, `platform`, `shutil`, `subprocess`, `sys`, `tarfile`, `tempfile`, `urllib`, `zipfile`, `pathlib`
- **被引用方**: `examples/_bootstrap.py`, `tests/test_artifact_workflow_and_binaries.py`, `tests/test_real_app_server_integration.py`

## 实现备注

- 下载策略按优先级排列：直接浏览器 URL -> GitHub API + Token -> `gh` CLI 工具
- 对 GitHub Token 的 401 错误会自动重试无认证请求，以处理过期或无效的 Token
- 版本比较使用规范化后的字符串（`-alpha.` -> `a`，`-beta.` -> `b`），兼容 PEP 440
- 安装后会验证实际安装的版本是否与请求版本一致，不一致则抛出异常
