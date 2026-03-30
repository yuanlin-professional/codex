# `command_canonicalization_tests.rs` — 测试模块

## 测试覆盖范围

测试命令规范化函数 `canonicalize_command_for_approval` 的行为，验证在审批流程中不同 shell 包装器（bash、zsh、powershell）下的命令被统一规范化，使得等价命令产生相同的规范形式。

## 测试场景列表

| 测试函数 | 场景描述 |
|----------|----------|
| `canonicalizes_word_only_shell_scripts_to_inner_command` | 仅含简单单词的 bash 脚本（如 `cargo test -p codex-core`）被提取为内部命令列表，`/bin/bash -lc` 与 `bash -lc` 以及多余空格均产生相同结果 |
| `canonicalizes_heredoc_scripts_to_stable_script_key` | 包含 heredoc 的复杂脚本被规范化为 `__codex_shell_script__` 占位键，`/bin/zsh` 与 `zsh` 产生相同结果 |
| `canonicalizes_powershell_wrappers_to_stable_script_key` | PowerShell 命令被规范化为 `__codex_powershell_script__` 占位键，`powershell.exe -NoProfile -Command` 与 `powershell -Command` 产生相同结果 |
| `preserves_non_shell_commands` | 非 shell 包装器命令（如直接调用 `cargo fmt`）保持原样不变 |
