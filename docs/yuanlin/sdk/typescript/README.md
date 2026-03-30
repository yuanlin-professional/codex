# sdk/typescript -- Codex TypeScript SDK

## 功能概述

`@openai/codex-sdk` 是 Codex 代理的 TypeScript SDK，用于将 Codex 嵌入工作流和应用程序。SDK 封装了 `codex` CLI（来自 `@openai/codex`），通过子进程方式启动 CLI，并在 stdin/stdout 上交换 JSONL 事件流。

主要特性包括：同步/异步会话管理、流式响应、结构化输出（JSON Schema / Zod）、图片附件、线程恢复、工作目录控制、环境变量定制、`--config` 覆盖等。

## 目录结构

```
sdk/typescript/
├── package.json          # npm 包定义，名称 @openai/codex-sdk
├── tsconfig.json         # TypeScript 编译配置
├── tsup.config.ts        # tsup 打包配置
├── eslint.config.js      # ESLint 配置
├── jest.config.cjs       # Jest 测试配置
├── README.md             # 英文说明文档
├── src/
│   ├── index.ts          # 包入口，统一导出
│   ├── codex.ts          # Codex 主类实现
│   ├── codexOptions.ts   # Codex 构造选项定义
│   ├── thread.ts         # Thread 类实现
│   ├── threadOptions.ts  # Thread 创建选项
│   ├── turnOptions.ts    # Turn 执行选项
│   ├── events.ts         # 事件类型定义
│   ├── items.ts          # Item 类型定义
│   ├── exec.ts           # CLI 子进程执行逻辑
│   └── outputSchemaFile.ts # 输出 schema 文件处理
├── samples/              # 示例代码
│   ├── helpers.ts        # 示例辅助函数
│   ├── basic_streaming.ts       # 基础流式示例
│   ├── structured_output.ts     # 结构化输出示例
│   └── structured_output_zod.ts # Zod schema 结构化输出
└── tests/                # 测试用例
```

## 依赖关系

- **运行时**：无运行时依赖（所有依赖均为 devDependencies）
- **开发依赖**：
  - TypeScript >= 5.9、tsup >= 8.5（打包）
  - Jest >= 29.7、ts-jest（测试）
  - ESLint >= 9.36、Prettier >= 3.6（代码风格）
  - zod >= 3.24、zod-to-json-schema >= 3.24（结构化输出支持）
  - @modelcontextprotocol/sdk >= 1.24（MCP 协议支持）
- **Node.js 版本**：>= 18
- **包管理器**：pnpm 10.29.3
- **许可证**：Apache-2.0

## 核心接口/API

| 接口 | 说明 |
|------|------|
| `new Codex(options?)` | 创建 SDK 客户端实例，可配置 `env`、`config`、`baseUrl` 等 |
| `codex.startThread(options?)` | 创建新会话线程，可指定 `workingDirectory`、`skipGitRepoCheck` |
| `codex.resumeThread(threadId)` | 恢复已有线程 |
| `thread.run(input, options?)` | 执行一个 turn 并缓冲到完成，返回 `turn.finalResponse` 和 `turn.items` |
| `thread.runStreamed(input, options?)` | 流式执行 turn，返回异步事件生成器 |
| `options.outputSchema` | 指定 JSON Schema 以获取结构化输出 |

### 事件类型（流式模式）

- `item.completed` -- 一个 item 处理完成
- `turn.completed` -- turn 完成，包含 usage 信息
