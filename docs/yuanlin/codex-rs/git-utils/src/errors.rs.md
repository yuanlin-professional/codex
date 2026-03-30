# `errors.rs` — Git 工具错误类型
定义 `GitToolingError` 枚举，涵盖 Git 命令失败、输出 UTF-8 解码错误、非 Git 仓库、非相对路径、路径逃逸、路径前缀剥离失败、walkdir 错误和 IO 错误等所有 git-utils 操作可能产生的错误类型。
