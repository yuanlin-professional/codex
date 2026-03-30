# `testCodex.ts` -- 测试用 Codex 客户端工厂

## 功能概述

`testCodex.ts` 提供了测试专用的 `Codex` 客户端创建函数。`createMockClient` 快速创建连接到本地 mock 代理的客户端，`createTestClient` 则提供更灵活的配置选项。两者都使用本地编译的 Codex 二进制文件（通过 `CODEX_EXEC_PATH` 环境变量或默认的 debug 构建路径），并自动配置 mock model provider 以拦截 API 请求。

## 关键类型/接口

| 名称 | 类型 | 说明 |
|------|------|------|
| `CreateTestClientOptions` | type | 测试客户端选项，含 apiKey、baseUrl、config、env、inheritEnv |
| `TestClient` | type | 返回类型，含 `client`（Codex 实例）和 `cleanup` 函数 |

## 关键函数/方法

| 函数 | 签名摘要 | 说明 |
|------|----------|------|
| `createMockClient` | `(url: string): TestClient` | 创建连接到指定 mock 代理 URL 的测试客户端 |
| `createTestClient` | `(options?): TestClient` | 创建可自定义配置的测试客户端 |
| `mergeTestConfig` | `(baseUrl?, config?): CodexConfigObject` | 合并测试配置，自动添加 mock provider 和禁用 plugins |
| `hasExplicitProviderConfig` | `(config?): boolean` | 检查配置中是否已显式设置 provider |
| `getCurrentEnv` | `(): Record<string, string>` | 获取当前环境变量（排除 originator override） |

## 依赖关系

- **引用的模块**: `node:path`、`../src/codex`（Codex 类）、`../src/codexOptions`（CodexConfigObject）
- **被引用方**: `tests/abort.test.ts`、`tests/run.test.ts`、`tests/runStreamed.test.ts`

## 实现备注

- `codexExecPath` 默认指向 `../../codex-rs/target/debug/codex`，即本地 Rust debug 构建。
- mock provider 配置使用 `wire_api: "responses"` 和 `supports_websockets: false`，确保请求走 SSE 模式到本地代理。
- 所有测试客户端默认禁用 `features.plugins`，防止后台插件同步与临时 CODEX_HOME 清理冲突。
- 环境变量处理会过滤掉 `CODEX_INTERNAL_ORIGINATOR_OVERRIDE`，避免测试间干扰。
