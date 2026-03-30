# sdk/python-runtime -- Codex CLI 运行时包

## 功能概述

`codex-cli-bin` 是为 `codex-app-server-sdk` Python SDK 提供的平台特定运行时包。它在发布流程中被暂存（staged），使得 SDK 可以精确锁定某个 Codex CLI 版本，而无需将平台二进制文件签入代码仓库。

该包仅以 wheel 格式发布，不提供 sdist 构建。构建时会自动拒绝 sdist 目标并标记为非纯 Python 包。

## 目录结构

```
sdk/python-runtime/
├── pyproject.toml     # 项目构建配置（hatchling），定义 wheel 打包规则
├── README.md          # 英文说明文档
├── hatch_build.py     # 自定义构建钩子：禁止 sdist、标记平台 wheel
└── src/
    └── codex_cli_bin/
        └── bin/       # 平台特定的 codex CLI 二进制文件（发布时暂存）
```

## 依赖关系

- **构建系统**：hatchling >= 1.24.0
- **Python 版本**：>= 3.10（支持 3.10 ~ 3.13）
- **被依赖方**：`codex-app-server-sdk` 的发布版本会 pin 到精确的 `codex-cli-bin==x.y.z` 版本
- **许可证**：Apache-2.0

## 核心接口/API

该包不提供 Python API。它的唯一作用是在 `src/codex_cli_bin/bin/` 下分发平台特定的 `codex` CLI 可执行文件，供 SDK 在运行时定位并调用。

构建钩子 `hatch_build.py` 中的 `RuntimeBuildHook` 类确保：
- 构建 sdist 时抛出 `RuntimeError`
- 将 `pure_python` 设为 `False`
- 启用 `infer_tag` 以自动推断平台标签
