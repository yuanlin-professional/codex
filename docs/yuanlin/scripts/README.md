# scripts -- 项目辅助脚本

## 功能概述

`scripts/` 目录包含 Codex 项目的顶层辅助脚本，覆盖代码质量检查、构建辅助、测试支持和发布流程等方面。这些脚本主要被 CI 工作流和开发者在本地开发中使用。

## 目录结构

```
scripts/
├── asciicheck.py                        # ASCII 字符检查
├── check_blob_size.py                   # Git blob 大小检查
├── check-module-bazel-lock.sh           # Bazel 模块锁文件一致性检查
├── debug-codex.sh                       # Codex 调试辅助脚本
├── install/                             # 安装脚本目录
├── mock_responses_websocket_server.py   # 模拟 WebSocket 响应服务器
├── readme_toc.py                        # README 目录生成/验证工具
├── run_tui_with_exec_server.sh          # 带执行服务器的 TUI 运行脚本
├── stage_npm_packages.py                # npm 包发布暂存脚本
└── test-remote-env.sh                   # 远程测试环境初始化脚本
```

## 各脚本说明

| 脚本 | 语言 | 说明 |
|------|------|------|
| `asciicheck.py` | Python | 检查指定文件是否包含非 ASCII 字符（允许白名单例外） |
| `check_blob_size.py` | Python | 检查 Git blob 大小是否超过阈值（默认 500KB），防止意外提交大文件 |
| `check-module-bazel-lock.sh` | Shell | 验证 Bazel MODULE.bazel.lock 文件与实际依赖的一致性 |
| `debug-codex.sh` | Shell | Codex CLI 调试辅助脚本 |
| `mock_responses_websocket_server.py` | Python | 在 `127.0.0.1:8765` 上启动 WebSocket 服务器，模拟 `/v1/responses` 端点 |
| `readme_toc.py` | Python | 验证（或通过 `--fix` 修复）Markdown 文件中 `<!-- Begin ToC -->` 和 `<!-- End ToC -->` 之间的目录 |
| `run_tui_with_exec_server.sh` | Shell | 启动带执行服务器的 TUI 进行交互测试 |
| `stage_npm_packages.py` | Python | 为发布暂存一个或多个 Codex npm 包 |
| `test-remote-env.sh` | Shell | 构建和初始化远程测试 Docker 环境 |

## 依赖关系

- **被调用方**：`.github/workflows/` 中的 CI 工作流、`justfile` 任务、开发者手动执行
- **外部依赖**：Python 3、Bash、Docker（远程测试）、websockets 库（mock server）
