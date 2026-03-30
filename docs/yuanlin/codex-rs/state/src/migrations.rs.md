# `migrations.rs` — SQLite 数据库迁移定义
该文件使用 `sqlx::migrate!` 宏声明了两个静态 `Migrator` 实例：`STATE_MIGRATOR`（从 `./migrations` 目录加载状态数据库迁移）和 `LOGS_MIGRATOR`（从 `./logs_migrations` 目录加载日志数据库迁移）。
