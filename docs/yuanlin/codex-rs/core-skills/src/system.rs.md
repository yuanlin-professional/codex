# `system.rs` — 系统技能安装与卸载辅助模块
该文件是 `codex_skills` crate 中系统技能安装/卸载功能的薄封装层。它重导出 `install_system_skills` 和 `system_cache_root_dir`，并额外提供 `uninstall_system_skills` 函数用于清理 `$CODEX_HOME/skills/.system` 目录下的缓存系统技能。
