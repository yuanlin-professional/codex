# `prompt_args.rs` -- 斜杠命令解析

`prompt_args.rs` 提供了 `parse_slash_name` 函数，用于解析形如 `/name <rest>` 的首行斜杠命令。函数返回 `(name, rest_after_name, rest_offset)` 三元组，其中 `rest_offset` 是去除前导空白后 rest 在原始行中的字节偏移。当行不以 `/` 开头或名称为空时返回 `None`。该函数被 ChatComposer 和 BottomPane 用于判断用户是否正在输入斜杠命令。
