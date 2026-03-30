# `write_schema_fixtures.rs` — Schema Fixture 重新生成 CLI

`write_schema_fixtures.rs` 是一个 CLI 二进制入口，用于重新生成 app-server 协议的 schema fixture 文件（位于 `schema/` 目录下的 `typescript/` 和 `json/` 子目录）。支持指定 schema 根目录、Prettier 格式化工具路径和实验性 API 选项。通常通过 `just write-app-server-schema` 命令调用。
