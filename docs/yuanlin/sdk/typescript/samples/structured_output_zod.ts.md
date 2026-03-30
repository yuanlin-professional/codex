# `structured_output_zod.ts` -- 结构化输出示例（Zod Schema）

`structured_output_zod.ts` 演示了使用 Zod 库定义 schema 并通过 `zod-to-json-schema` 转换为 JSON Schema 后传递给 Codex SDK 的结构化输出功能。与 `structured_output.ts` 功能相同，但展示了更符合 TypeScript 生态的 Zod 工作流：先用 `z.object()` 定义类型安全的 schema，再通过 `zodToJsonSchema(schema, { target: "openAi" })` 转换为 OpenAI 兼容的 JSON Schema 格式。
