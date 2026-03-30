# `platform.rs` — 跨平台符号链接创建
提供 `create_symlink` 函数，在 Unix 上调用 `std::os::unix::fs::symlink`，在 Windows 上根据源文件类型区分 `symlink_dir` 和 `symlink_file`。不支持其他平台（编译期错误）。
