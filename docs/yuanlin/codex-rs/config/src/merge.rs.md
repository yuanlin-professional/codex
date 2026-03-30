# `merge.rs` — 一句话概述

提供 `merge_toml_values(base, overlay)` 函数，将 TOML overlay 值递归合并到 base 值中。对于 Table 类型递归合并子键，对于其他类型（包括 Array）overlay 直接替换 base，实现配置层的从低优先级到高优先级的逐层覆盖。
