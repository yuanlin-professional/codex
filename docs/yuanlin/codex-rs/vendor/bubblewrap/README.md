# vendor/bubblewrap - Bubblewrap 沙箱工具（第三方）

## 功能概述

此目录包含 Bubblewrap 的源码副本，Bubblewrap 是一个 Linux 用户空间沙箱工具，Codex 使用它在 Linux 平台上实现命令执行沙箱化。它利用 Linux 内核的用户命名空间（user namespaces）来创建隔离的执行环境。

## 目录结构

| 子目录/文件 | 说明 |
|-------------|------|
| `bubblewrap.c` | 主程序源码 |
| `bind-mount.c/h` | 绑定挂载实现 |
| `network.c/h` | 网络隔离实现 |
| `utils.c/h` | 工具函数 |
| `packaging/` | RPM 打包配置 |
| `ci/` | CI 构建脚本 |
| `completions/` | Shell 自动补全（bash/zsh） |
| `demos/` | 使用示例脚本 |
| `tests/` | 测试套件 |
| `.github/workflows/` | CI 工作流 |
| `meson.build` | Meson 构建配置 |
