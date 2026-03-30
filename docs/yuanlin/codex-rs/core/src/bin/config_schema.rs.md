# `bin/config_schema.rs` — 配置文件 JSON Schema 生成工具

该文件是一个独立的 CLI 二进制程序，用于生成 `config.toml` 对应的 JSON Schema 文件。使用 `clap` 解析命令行参数，支持通过 `-o`/`--out` 指定输出路径，默认输出到 crate 根目录的 `config.schema.json`。核心逻辑委托给 `codex_core::config::schema::write_config_schema` 完成 schema 的生成和写入。
