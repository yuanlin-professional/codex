# `default_client.rs` — 默认 HTTP 客户端构建

## 功能概述

构建默认的 reqwest HTTP 客户端，支持自定义 CA 证书加载、代理配置、超时设置和 TLS 配置。提供全局 originator 和 residency 设置函数。

## 依赖关系

- **被引用方**: core 模块的 HTTP 请求
