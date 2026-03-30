# `write_hooks_schema_fixtures.rs` -- 钩子 JSON Schema fixture 生成工具

该文件是一个简单的 CLI 二进制入口，调用 `codex_hooks::write_schema_fixtures` 将钩子系统的 JSON Schema fixture 文件生成到指定目录（默认为 crate 目录下的 `schema` 子目录）。用于开发和 CI 中确保 schema 文件与代码定义保持同步。
