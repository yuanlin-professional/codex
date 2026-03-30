# `main.rs` -- apply_patch 可执行文件入口

本文件是 `codex-apply-patch` 二进制 crate 的 main 入口，仅包含一行代码，直接委托给 `codex_apply_patch::main()` 执行。该函数定义在 `standalone_executable.rs` 中，返回类型为 `!`（永不返回），通过 `std::process::exit` 退出。
