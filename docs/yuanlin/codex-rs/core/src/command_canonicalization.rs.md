# `command_canonicalization.rs` — 命令规范化用于审批缓存匹配

该文件提供 `canonicalize_command_for_approval` 函数，用于将命令 argv 规范化以确保审批缓存匹配的稳定性。它解决了不同 shell 包装路径（如 `/bin/bash -lc` 与 `bash -lc`）导致同一命令产生不同审批缓存键的问题。函数优先尝试解析为单条简单 shell 命令；若失败则尝试提取 bash 或 powershell 脚本内容，分别使用 `__codex_shell_script__` 和 `__codex_powershell_script__` 前缀标记；若都无法识别，则原样返回命令。对应的测试位于 `command_canonicalization_tests.rs` 文件中。
