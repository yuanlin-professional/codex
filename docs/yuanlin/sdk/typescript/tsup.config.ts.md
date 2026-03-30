# `tsup.config.ts` -- 构建配置

`tsup.config.ts` 是 Codex TypeScript SDK 的 tsup 构建配置文件。配置入口为 `src/index.ts`，输出格式为 ESM，启用 TypeScript 声明文件（dts）和 sourcemap 生成，不启用代码压缩，目标运行时为 Node.js 18，构建前清理输出目录。
