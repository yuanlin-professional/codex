# `custom_ca.rs` — 自定义 CA 证书管理

## 功能概述

管理自定义 CA（证书颁发机构）证书的加载和验证。支持从多种来源加载 CA 证书：NODE_EXTRA_CA_CERTS、SSL_CERT_FILE 环境变量、系统证书存储等。提供证书探测功能用于诊断 TLS 连接问题。

## 依赖关系

- **被引用方**: `codex-client/src/default_client.rs`
