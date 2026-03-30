# `check-module-bazel-lock.sh` -- 检查 Bazel 模块锁文件是否最新

该脚本在 CI 中运行 `bazel mod deps --lockfile_mode=error`，验证 `MODULE.bazel.lock` 是否与当前 `MODULE.bazel` 配置同步。若锁文件过期，脚本会提示开发者执行 `just bazel-lock-update` 并提交更新后的锁文件，然后以非零退出码退出。
