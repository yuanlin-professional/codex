# `mod_tests.rs` — 测试模块

## 测试覆盖范围
验证 `maybe_wrap_shell_lc_with_snapshot` 函数在各种场景下的行为，包括不同 shell 类型、单引号转义、尾部参数保留、cwd 匹配检测以及环境变量覆盖的优先级处理。

## 测试场景列表
| 测试函数 | 场景描述 |
|----------|----------|
| `maybe_wrap_shell_lc_with_snapshot_bootstraps_in_user_shell` | Zsh 用户 shell 下正确包装 bash -lc 命令 |
| `maybe_wrap_shell_lc_with_snapshot_escapes_single_quotes` | 正确处理命令中的单引号转义 |
| `maybe_wrap_shell_lc_with_snapshot_uses_bash_bootstrap_shell` | Bash 作为引导 shell 时的包装行为 |
| `maybe_wrap_shell_lc_with_snapshot_uses_sh_bootstrap_shell` | sh 作为引导 shell 时的包装行为 |
| `maybe_wrap_shell_lc_with_snapshot_preserves_trailing_args` | 保留命令尾部参数（如 $0, $1） |
| `maybe_wrap_shell_lc_with_snapshot_skips_when_cwd_mismatch` | cwd 不匹配时跳过包装，原样返回 |
| `maybe_wrap_shell_lc_with_snapshot_accepts_dot_alias_cwd` | 接受 `.` 别名的 cwd 路径匹配 |
| `maybe_wrap_shell_lc_with_snapshot_restores_explicit_override_precedence` | 显式覆盖变量优先于快照变量 |
| `maybe_wrap_shell_lc_with_snapshot_keeps_snapshot_path_without_override` | 无显式覆盖时保留快照中的 PATH |
| `maybe_wrap_shell_lc_with_snapshot_applies_explicit_path_override` | 显式 PATH 覆盖生效 |
| `maybe_wrap_shell_lc_with_snapshot_does_not_embed_override_values_in_argv` | 敏感值不会嵌入到 argv 中 |
| `maybe_wrap_shell_lc_with_snapshot_preserves_unset_override_variables` | 未设置的覆盖变量在快照后保持 unset 状态 |
