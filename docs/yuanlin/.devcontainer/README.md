# .devcontainer -- 容器化开发环境

## 功能概述

`.devcontainer/` 目录提供 Codex 项目的容器化开发环境配置，支持通过 Docker 和 VS Code Dev Containers 在容器中进行 Rust 开发。这对于在 macOS 主机上验证 Linux 构建尤为有用。

容器基于 Ubuntu 24.04，预装了 Rust 工具链（含 musl 目标）、构建工具（build-essential、clang、cmake）和开发辅助工具（clippy、rustfmt、just）。

## 目录结构

```
.devcontainer/
├── devcontainer.json    # VS Code Dev Container 配置文件
├── Dockerfile           # 容器镜像定义（Ubuntu 24.04 + Rust 工具链）
└── README.md            # 英文使用说明
```

## 配置说明

### Dockerfile
- **基础镜像**：Ubuntu 24.04
- **安装内容**：
  - 构建依赖：build-essential、curl、git、ca-certificates、pkg-config、libcap-dev、clang、musl-tools、libssl-dev、just
  - Rust 工具链：通过 rustup 安装 minimal profile
  - Rust 目标：aarch64-unknown-linux-musl
  - Rust 组件：clippy、rustfmt
- **用户**：ubuntu（UID 1000）
- **工作目录**：/workspace

### devcontainer.json
- **平台**：linux/arm64（默认）
- **环境变量**：`RUST_BACKTRACE=1`、`CARGO_TARGET_DIR` 指向独立目录避免与主机产物冲突
- **VS Code 扩展**：rust-analyzer、even-better-toml

## 使用方式

### Docker 直接使用（x64）
```shell
docker build --platform=linux/amd64 -t codex-linux-dev ./.devcontainer
docker run --platform=linux/amd64 --rm -it \
  -e CARGO_TARGET_DIR=/workspace/codex-rs/target-amd64 \
  -v "$PWD":/workspace -w /workspace/codex-rs codex-linux-dev
```

### VS Code Dev Container
VS Code 会自动识别 `devcontainer.json` 文件，提供"在容器中打开"的选项。

## 依赖关系

- **外部依赖**：Docker 运行时、VS Code Remote - Containers 扩展
- **被引用方**：开发者本地开发环境、CI 中的容器化构建
