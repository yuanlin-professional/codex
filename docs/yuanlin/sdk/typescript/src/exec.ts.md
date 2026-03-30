# `exec.ts` -- Codex CLI 进程执行器

## 功能概述

`exec.ts` 是 SDK 与 Codex CLI 二进制文件之间的桥梁。它负责定位平台对应的 Codex 可执行文件、构建 CLI 参数、启动子进程并以异步生成器方式逐行产出 JSONL 输出。同时包含配置对象到 TOML `--config` 标志的序列化逻辑，以及跨平台（Linux/macOS/Windows，x64/arm64）的二进制查找逻辑。

## 关键类型/接口

| 名称 | 类型 | 说明 |
|------|------|------|
| `CodexExecArgs` | type | 执行参数，包含 input、model、sandboxMode、signal 等所有可选项 |
| `CodexExec` | class | 执行器类，封装 CLI 调用细节 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `CodexExec.constructor` | `(executablePath?, env?, configOverrides?)` | 初始化执行器，定位 Codex 二进制路径 |
| `CodexExec.run` | `(args: CodexExecArgs): AsyncGenerator<string>` | 启动子进程执行并逐行 yield JSONL 输出 |
| `serializeConfigOverrides` | `(config: CodexConfigObject): string[]` | 将嵌套配置对象序列化为 `key=value` TOML 字符串数组 |
| `flattenConfigOverrides` | `(value, prefix, overrides): void` | 递归扁平化配置对象为点分路径 |
| `toTomlValue` | `(value, path): string` | 将 JS 值转换为 TOML 字面量字符串 |
| `findCodexPath` | `(): string` | 根据当前平台/架构查找对应的 Codex 二进制文件路径 |
| `formatTomlKey` | `(key: string): string` | 格式化 TOML 键名（裸键或引号键） |
| `isPlainObject` | `(value): boolean` | 判断值是否为普通对象 |

## 依赖关系

- **引用的模块**: `node:child_process`（spawn）、`node:path`、`node:readline`、`node:module`（createRequire）、`./codexOptions`（CodexConfigObject/CodexConfigValue）、`./threadOptions`（SandboxMode 等枚举）
- **被引用方**: `./codex.ts`（创建 CodexExec 实例）、`./thread.ts`（调用 run 方法）

## 实现备注

- 使用 `spawn` 启动子进程，通过 `stdin` 写入用户输入，通过 `readline` 逐行读取 `stdout`。
- 支持 `AbortSignal`，通过 spawn 的 `signal` 选项实现取消。
- 环境变量处理：若提供 `envOverride` 则完全使用自定义环境，否则继承 `process.env`。始终设置 `CODEX_INTERNAL_ORIGINATOR_OVERRIDE` 标识来源为 TypeScript SDK。
- `findCodexPath` 通过 npm 包解析机制定位平台特定包 `@openai/codex-{platform}-{arch}` 中的二进制文件。
- 支持 6 个平台组合：Linux (x64/arm64)、macOS (x64/arm64)、Windows (x64/arm64)。
- `stderr` 收集后在进程非零退出时作为错误消息的一部分抛出。
