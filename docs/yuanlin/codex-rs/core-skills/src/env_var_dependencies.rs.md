# `env_var_dependencies.rs` — 技能环境变量依赖收集
该文件提供 `collect_env_var_dependencies` 函数，从已提及的技能元数据中提取类型为 `env_var` 的工具依赖项。返回包含技能名称、环境变量名称和描述的 `SkillDependencyInfo` 列表，用于在运行时检查所需环境变量是否已设置。
