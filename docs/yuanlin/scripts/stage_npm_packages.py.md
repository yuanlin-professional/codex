# `stage_npm_packages.py` -- 为发布暂存 Codex npm 包

## 功能概述

该脚本用于 Codex 的发布流程，负责将一个或多个 npm 包构建并打包为 `.tgz` 归档文件。它会根据包名展开平台特定子包，从 GitHub Actions 的 rust-release 工作流中下载原生二进制组件（如 codex、rg），然后调用 `build_npm_package.py` 脚本逐个构建并打包。脚本支持指定发布版本、自定义输出目录，以及复用已有的工作流 URL。

## 关键函数

| 函数 | 说明 |
|------|------|
| `main()` | 入口函数，协调整体流程：解析参数、下载原生组件、逐包构建打包 |
| `parse_args()` | 解析命令行参数：`--release-version`、`--package`（可多次）、`--workflow-url`、`--output-dir`、`--keep-staging-dirs` |
| `collect_native_components(packages)` | 根据包名收集所需的原生组件集合 |
| `expand_packages(packages)` | 将逻辑包名展开为实际的平台子包列表（去重） |
| `resolve_release_workflow(version)` | 通过 `gh run list` 查找指定版本对应的 rust-release 工作流信息 |
| `resolve_workflow_url(version, override)` | 获取工作流 URL，支持手动覆盖或自动查找 |
| `install_native_components(workflow_url, components, vendor_root)` | 调用 `install_native_deps.py` 从工作流产物中下载原生组件 |
| `run_command(cmd)` | 打印并执行 shell 命令 |
| `tarball_name_for_package(package, version)` | 根据包名和版本生成 `.tgz` 归档文件名 |

## 使用方式

```bash
# 暂存单个包
python scripts/stage_npm_packages.py \
  --release-version 0.1.0 \
  --package codex

# 暂存多个包
python scripts/stage_npm_packages.py \
  --release-version 0.1.0 \
  --package codex \
  --package codex-cli

# 使用已有工作流 URL，保留临时目录
python scripts/stage_npm_packages.py \
  --release-version 0.1.0 \
  --package codex \
  --workflow-url https://github.com/openai/codex/actions/runs/12345 \
  --keep-staging-dirs
```

## 实现备注

- 动态导入 `codex-cli/scripts/build_npm_package.py` 模块，获取 `PACKAGE_NATIVE_COMPONENTS`、`PACKAGE_EXPANSIONS` 和 `CODEX_PLATFORM_PACKAGES` 配置映射。
- 原生组件下载到 `RUNNER_TEMP`（CI 环境）或系统临时目录，完成后自动清理（除非指定 `--keep-staging-dirs`）。
- 若解析到工作流的 `headSha`，会提示用户 checkout 到对应提交以确保代码一致性。
- 默认输出目录为项目根目录下的 `dist/npm`。
