# `sed_command.rs` — sed 命令安全性验证

`parse_sed_command` 函数对 sed 命令字符串进行安全性验证。目前仅支持 `N,Mp` 格式的行范围打印命令（如 `122,202p`），即逗号分隔的两个正整数后跟 `p` 后缀。其他格式的 sed 命令将返回 `SedCommandNotProvablySafe` 错误，表明无法证明其安全性。
