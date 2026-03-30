# `local.rs` — 本地密钥存储

## 功能概述

实现基于文件系统的本地密钥存储，在 `CODEX_HOME` 目录下管理认证凭证。提供 API key 和 ChatGPT token 的加载、保存和清除功能。

## 依赖关系

- **被引用方**: `secrets/src/lib.rs`
