# `shell_detect.rs` — Shell 类型自动检测

该文件提供 `detect_shell_type` 函数，根据给定的 shell 路径自动检测 shell 类型。支持识别 `zsh`、`sh`、`cmd`、`bash`、`pwsh`、`powershell` 等名称。对于完整路径（如 `/bin/bash`），函数会递归提取文件名部分（`file_stem`）再次尝试匹配，从而同时支持简短名称和完整路径。无法识别时返回 `None`。
