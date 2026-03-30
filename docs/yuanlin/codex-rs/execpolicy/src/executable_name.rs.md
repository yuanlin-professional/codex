# `executable_name.rs` — 可执行文件名标准化

`executable_lookup_key` 函数将可执行文件名标准化为查找键：在 Windows 上移除 `.exe`、`.cmd`、`.bat`、`.com` 后缀并转为小写；在非 Windows 平台上直接返回原名。`executable_path_lookup_key` 从完整路径中提取文件名后调用标准化函数。这两个函数用于在策略匹配时将绝对路径的可执行文件解析为基名进行规则查找。
