# `mention_counts.rs` — 技能名称提及计数统计
该文件提供 `build_skill_name_counts` 函数，用于统计每个技能名称在启用技能列表中出现的次数（精确匹配和 ASCII 小写匹配两种计数）。排除了 `disabled_paths` 中标记为禁用的技能。用于检测技能名称歧义（同名技能冲突）场景。
