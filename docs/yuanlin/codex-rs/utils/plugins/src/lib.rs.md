# `lib.rs` — 插件路径解析与 mention 符号入口

本文件是 `plugins` 子 crate 的入口模块，声明并重新导出以下子模块：

- `mention_syntax`：工具/插件明文提及符号
- `plugin_namespace`：插件命名空间解析

导出 `PLUGIN_MANIFEST_PATH` 常量和 `plugin_namespace_for_skill_path` 函数。
