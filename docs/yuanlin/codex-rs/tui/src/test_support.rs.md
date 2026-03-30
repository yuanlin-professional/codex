# `test_support.rs` — 测试辅助路径工具

提供 `PathExt` 和 `PathBufExt` trait 以及 `test_path_display` 函数，
用于在测试中将路径转换为 `AbsolutePathBuf` 并生成平台无关的显示字符串。
