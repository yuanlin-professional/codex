# `install.sh` -- Codex CLI 一键安装脚本

## 功能概述

该脚本为 macOS 和 Linux 用户提供一键安装 Codex CLI 的能力。它自动检测当前操作系统和 CPU 架构（包括 Rosetta 2 转译检测），从 GitHub Releases 下载对应平台的预构建二进制包，解压后将 `codex` 和 `rg`（ripgrep）安装到用户目录，并自动将安装路径添加到 shell 的 PATH 配置中。支持安装指定版本或自动获取最新版本。

## 关键函数

| 函数 | 说明 |
|------|------|
| `normalize_version()` | 标准化版本号格式，去除 `v` 或 `rust-v` 前缀 |
| `download_file(url, output)` | 使用 curl 或 wget 下载文件到指定路径 |
| `download_text(url)` | 使用 curl 或 wget 获取远程文本内容 |
| `add_to_path()` | 检测 `$INSTALL_DIR` 是否已在 PATH 中，若未配置则写入对应 shell 配置文件（`.zshrc`/`.bashrc`/`.profile`） |
| `release_url_for_asset(asset, version)` | 拼接 GitHub Releases 的下载 URL |
| `require_command(cmd)` | 检查必需命令是否存在，缺失时退出 |
| `resolve_version()` | 解析版本号：若为 `latest` 则通过 GitHub API 获取最新发布版本 |
| `cleanup()` | 清理临时下载目录 |

## 使用方式

```bash
# 安装最新版本
curl -fsSL https://raw.githubusercontent.com/openai/codex/main/scripts/install/install.sh | sh

# 安装指定版本
curl -fsSL https://raw.githubusercontent.com/openai/codex/main/scripts/install/install.sh | sh -s -- 0.1.0

# 自定义安装目录
CODEX_INSTALL_DIR=/usr/local/bin curl -fsSL ... | sh
```

## 实现备注

- 默认安装目录为 `$HOME/.local/bin`，可通过 `CODEX_INSTALL_DIR` 环境变量覆盖。
- macOS 上通过 `sysctl -n sysctl.proc_translated` 检测 Rosetta 2 转译，确保在 Intel 模式终端下也能安装 ARM64 版本。
- 支持 4 种平台组合：`darwin-arm64`、`darwin-x64`、`linux-arm64`、`linux-x64`。
- 使用 `trap` 在退出时自动清理临时目录。
- 安装完成后根据 PATH 配置情况给出不同的后续操作提示。
