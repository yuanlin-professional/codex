# `test-remote-env.sh` -- 设置远程环境 Docker 容器用于集成测试

## 功能概述

该脚本为 `codex-rs` 的远程环境集成测试提供辅助设置功能。它必须通过 `source` 方式加载（而非直接执行），加载后会编译 `codex-exec-server` 二进制文件，启动一个 Ubuntu 24.04 Docker 容器作为远程测试环境，并将容器名称导出到 `CODEX_TEST_REMOTE_ENV` 环境变量中。测试完成后，用户可调用 `codex_remote_env_cleanup` 函数清理容器。

## 关键函数

| 函数 | 说明 |
|------|------|
| `is_sourced()` | 检测脚本是否通过 `source` 方式加载，直接执行时会报错退出 |
| `setup_remote_env()` | 核心设置函数：检查 docker/cargo 依赖 -> 编译 exec-server -> 启动 Docker 容器 -> 导出环境变量 |
| `codex_remote_env_cleanup()` | 清理函数：强制删除测试容器并取消环境变量 |

## 使用方式

```bash
# 加载脚本并设置环境
source scripts/test-remote-env.sh

# 运行远程环境相关测试
cd codex-rs
cargo test -p codex-core --test all remote_env_connects_creates_temp_dir_and_runs_sample_script

# 测试完成后清理
codex_remote_env_cleanup
```

## 实现备注

- 容器名称格式为 `codex-remote-test-env-local-{时间戳}-{随机数}`，可通过 `CODEX_TEST_REMOTE_ENV_CONTAINER_NAME` 环境变量覆盖。
- 在启动新容器前会先尝试删除同名旧容器（`docker rm -f`）。
- 使用 `set +o` 保存和恢复调用方的 shell 选项，避免 `set -euo pipefail` 影响调用方的 shell 环境。
- 要求本地安装 Docker（Colima 或 Docker Desktop）且守护进程正在运行。
